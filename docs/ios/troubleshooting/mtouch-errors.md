---
title: Erreurs de Xamarin.iOS
description: Ce document décrit les différents messages d’erreur générés par mtouch, l’outil utilisé pour regrouper les applications Xamarin.iOS. Les erreurs sont répertoriées par le code et étant données une description complète.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 6d7684df771f24ecf9126f472e7d799eea4a5431
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832702"
---
# <a name="xamarinios-errors"></a>Erreurs de Xamarin.iOS

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx : messages d’erreur mtouch

Par exemple, paramètres, environnement, manque d’outils.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MT0000 : Erreur inattendue : Veuillez remplir un rapport de bogue à https://github.com/xamarin/xamarin-macios/issues/new

Une condition d’erreur inattendue s’est produite. Veuillez [un rapport de bogue](https://github.com/xamarin/xamarin-macios/issues/new) avec autant d’informations que possible, notamment :

* Build complète des journaux, avec un maximum de commentaires (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**) ;
* Un cas de test minimale pour reproduire l’erreur ; et
* Toutes les informations de version

Le moyen le plus simple pour obtenir des informations de version exacte consiste à utiliser le **Visual Studio pour Mac** menu, **sur Visual Studio pour Mac** élément, **afficher les détails** bouton et copier/coller le informations de version (vous pouvez utiliser la **copier les informations** bouton).

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001 : '-nom_unité ' a été fourni sans aucune action spécifique à l’appareil

Il s’agit d’un avertissement est émis si - nom_unité est passé à mtouch lorsque aucune action spécifique au périphérique (-logdev/installdev/killdev/launchdev /-listapps) a été demandée.

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002 : La variable d’environnement n’a pas pu être analyser *.

Cette erreur se produit si vous essayez de définir une clé d’environnement non valide = paire de variables de valeur. Le format correct est : `mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003 : Nom de l’application ' * .exe ' est en conflit avec un nom d’assembly (.dll) produit ou le Kit de développement logiciel.

Nom de l’assembly exécutable et le nom de l’application ne peut pas correspondre au nom d’une dll dans l’application. Modifiez le nom de votre fichier exécutable.

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004 : Nouvelle logique de refcounting nécessite SGen doit être activé en trop.

Si vous activez l’extension de refcounting vous devez également activer le RÉCUPÉRATEUR de mémoire SGen dans des options Build iOS du projet (onglet Avancé).

En commençant par Xamarin.iOS 7.2.1 cette exigence a été levée, la nouvelle logique de refcounting peut être activée avec Boehm et SGen Garbage Collectors.

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005 : Le répertoire de sortie * n’existe pas.

Créez le répertoire.

Cette erreur n’est pas générée plus, mtouch crée automatiquement le répertoire s’il n’existe pas.

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006 : Il n’existe aucune plateforme de développement à *, utilisez--platform = PLAT pour spécifier le Kit de développement.

Xamarin.iOS ne peut pas trouver le répertoire du Kit de développement logiciel à l’emplacement mentionné dans le message d’erreur. Vérifiez que le chemin d’accès est correct.

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007 : L’assembly racine * n’existe pas.

Xamarin.iOS ne peut pas trouver l’assembly à l’emplacement mentionné dans le message d’erreur. Vérifiez que le chemin d’accès est correct.

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008 : Vous devez fournir un assemblys de racine assembly # uniquement, trouvé : *.

Plusieurs assemblys racine a été passé à mtouch, peuvent être assembly qu’une seule racine.

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009 : Erreur lors du chargement des assemblys : *.

Une erreur s’est produite lors du chargement des assemblys les références d’assembly racine. Plus d’informations peuvent être fournies dans la sortie de génération.

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010 : Les arguments de ligne de commande n’a pas pu être analyser : *.

Une erreur s’est produite lors de l’analyse les arguments de ligne de commande. Veuillez vérifier qu’ils sont tous corrects.

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011 : * a été développée pour un runtime plus récent (*) que MonoTouch prend en charge.

Cet avertissement est généralement signalé car le projet a une référence à une bibliothèque de classes qui n’a pas été générée à l’aide de la BCL Xamarin.iOS.

La même façon qu’une application en utilisant le Kit de développement logiciel de .NET 4.0 peut ne pas fonctionne sur un système uniquement prise en charge de .NET 2.0, une bibliothèque créée à l’aide de .NET 4.0 peut ne pas fonctionne sur Xamarin.iOS, il peut utiliser les API non présent sur Xamarin.iOS.

La solution générale consiste à créer la bibliothèque sous la forme d’une bibliothèque de classes Xamarin.iOS. Cela est possible en créant un nouveau projet de bibliothèque de classes Xamarin.iOS et lui ajouter tous les fichiers sources. Si vous n’avez pas le code source pour la bibliothèque, vous devez contacter le fournisseur et demander qu’ils fournissent une version compatible avec Xamarin.iOS de leur bibliothèque.

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012 : Données incomplètes sont fournies pour l’exécution *.

Cette erreur est signalée non plus dans la version actuelle de Xamarin.iOS.

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013 : Prise en charge le profilage de requiert sgen doit être activé en trop.

SGen (--sgen) doit être activée si le profilage (--profilage) est activée.

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014 : IOS * SDK ne prend pas en charge la création d’applications ciblant *.

Cela peut se produire dans les circonstances suivantes :

*  ARMv6 est activé et Xcode 4.5 ou version ultérieure est installé.
*  ARMv7s est activé et Xcode 4.4 ou version antérieure est installé.

Vérifiez que la version installée de Xcode prend en charge les architectures sélectionnées.

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x8664--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015 : ABI non valide : *. ABI prises en charge est : i386, x86_64, armv7, armv7 + llvm, armv7 + llvm + thumb2, armv7s, armv7s + llvm, armv7s + llvm + thumb2, arm64 et arm64 + llvm.

Une ABI non valide a été passée à mtouch. Veuillez spécifier une ABI valide.

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016 : L’option * a été déconseillée.

L’option mtouch mentionné a été déconseillée et sera ignorée.

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017 : Vous devez fournir un assembly de la racine.

Il est nécessaire de spécifier un assembly de racine (généralement le principal exécutable) lors de la création d’une application.

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018 : Argument de ligne de commande inconnu : *.

Mtouch ne reconnaît pas l’argument de ligne de commande indiqué dans le message d’erreur.

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019 : Seul--[journal | installer | kill | lancez] développement ou--[lancer | déboguer] sim option peut être utilisée.

Il existe plusieurs options pour mtouch ne peuvent pas être utilisés simultanément :

-  --logdev
-  --installdev
-  --killdev
-  --launchdev
-  --launchdebug
-  --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020 : Les options valides pour '\*« sont »\*».

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021 : Impossible de compiler à l’aide de gcc / g ++ (--utilisez-gcc) lors de l’utilisation du bureau d’enregistrement statique (il s’agit de la valeur par défaut lors de la compilation pour l’appareil). Supprimez le--utilisez-gcc indicateur ou utiliser le bureau d’enregistrement dynamique (--registrar : dynamiques).

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022 : L’options '--non pris en charge--enable-génériques-dans-bureau d’enregistrement' et '--registrar » ne sont pas compatibles.

Supprimer les deux options `--unsupported--enable-generics-in-registrar` et `--registrar`. En commençant par Xamarin.iOS 7.2.1 du bureau d’enregistrement par défaut prend en charge les génériques.

Cette erreur n’est plus affichée (l’argument de ligne de commande `--unsupported--enable-generics-in-registrar` a été supprimé de mtouch).

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023 : Nom de l’application ' * .exe ' est en conflit avec un autre assembly de l’utilisateur.

Nom de l’assembly exécutable et le nom de l’application ne peut pas correspondre au nom d’une dll dans l’application. Modifiez le nom de votre fichier exécutable.

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024 : Impossible de trouver le fichier requis ' *'.

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025 : Aucune version du Kit de développement logiciel a été fournie. Veuillez ajouter `--sdk=X.Y` pour spécifier quels iOS SDK doit être utilisé pour générer votre application.

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026 : L’argument de ligne de commande n’a pas pu être analyser ' *': *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027 : Les options'\*'et'\*' ne sont pas compatibles.

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028 : Impossible d’activer le graphique à secteurs (-graphique à secteurs) lorsque vous ciblez iOS 4.1 ou une version antérieure. Désactivez le graphique à secteurs (-graphique à secteurs : false) ou la valeur est la cible de déploiement au moins iOS 4.2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029 : REPL (--enable-repl) est uniquement pris en charge dans le simulateur (--sim).

REPL n’est possible que si vous créez pour le simulateur. Cela signifie que si vous transmettez `--enable-repl` à mtouch, vous devez également passer `--sim`.

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030 : Le nom du fichier exécutable (\*) et le nom de l’application (\*) sont différentes, cela peut empêcher les journaux d’incidents de l’obtention des correctement.

Lorsque Xcode symbolicates (traduit les adresses de mémoire pour les noms de fonction et le fichier/numéros de ligne) le processus peut échouer si l’application et le fichier exécutable ont des noms différents (sans l’extension).

Pour résoudre cette modifiez « Application Name » dans les options de Build/iOS Application du projet, ou modifiez Nom de l’Assembly dans les options de sortie de la génération du projet.

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031 : Les arguments de ligne de commande '--récupération enable-en arrière-plan ' et '--lancement pour récupération en arrière-plan » nécessitent '--launchsim' trop.

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032 : L’option '--debugtrack' est ignorée sauf si '--debug » est également spécifié.

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033 : Un projet Xamarin.iOS doit faire référence monotouch.dll ou Xamarin.iOS.dll

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034 : Ne peut pas inclure 'monotouch.dll' et 'Xamarin.iOS.dll' dans le même projet Xamarin.iOS - '\*' est référencée explicitement, alors que '\*' est référencé par ' *'.

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036 : Impossible de lancer un * simulateur pour une * application. Activez l’architecture(s) correct dans des options Build iOS votre projet (page Avancé).

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x8664"></a>MT0037 : monotouch.dll est compatible 64 bits pas. Xamarin.iOS.dll de référence, ou ne sont pas générés pour une architecture 64 bits (ARM64 et/ou x86_64).

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038 : Les bureaux d’enregistrement ancien (--bureau d’enregistrement : oldstatic | olddynamic) ne sont pas pris en charge lors du référencement Xamarin.iOS.dll.

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039 : Applications qui ciblent ARMv6 ne peut pas référencer Xamarin.iOS.dll.

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040 : Impossible de trouver l’assembly '\*', référencé par'\*'.

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041 : Impossible de référencer 'monotouch.dll' et 'Xamarin.iOS.dll'.

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042 : Aucune référence à monotouch.dll ou Xamarin.iOS.dll a été trouvé. Une référence à monotouch.dll sera ajoutée.

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043 : Le RÉCUPÉRATEUR de mémoire Boehm est actuellement pas pris en charge faisant référence à 'Xamarin.iOS.dll'. Le RÉCUPÉRATEUR de mémoire SGen a été sélectionné à la place.

Le RÉCUPÉRATEUR de mémoire SGen est pris en charge avec les projets unifiée. N’Assurez-vous qu’aucun indicateur mtouch supplémentaires spécifiant Boehm en tant que le garbage collector.

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044 :--listsim est uniquement pris en charge avec Xcode 6.0 ou version ultérieure.

Installer une version plus récente de Xcode.

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045 :--extension est uniquement pris en charge lors de l’utilisation de l’iOS SDK 8.0 (ou version ultérieure).

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047 : La cible de déploiement minimale pour les applications d’unifiée est 5.1.1, la cible de déploiement actuel est ' *'. Sélectionnez une cible de déploiement plus récente dans iOS de votre projet options de l’Application.

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049 : *.framework est pris en charge uniquement si la cible de déploiement est 8.0 ou version ultérieure. * fonctionnalités ne fonctionnent pas correctement.

Le framework spécifié n’est pas pris en charge dans la cible de déploiement fait référence à la version d’iOS. Mettre à jour la cible de déploiement vers une nouvelle version d’iOS, soit supprimer l’utilisation du framework spécifié de l’application.

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051 : Xamarin.iOS * nécessite Xcode 5.0 ou version ultérieure. La version actuelle de Xcode (trouvée dans *) est *.

Installez un Xcode plus récente.

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052 : Aucune commande n’est spécifiée.

Aucune action n’a été spécifiée pour mtouch.

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054 : Impossible de rendre canonique le chemin d’accès ' *': *

Il s’agit d’une erreur interne. Si vous voyez cette erreur, veuillez soumettre un bogue [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055 : Le chemin d’accès de Xcode ' *' n’existe pas.

Le chemin d’accès de Xcode passé à l’aide de `--sdkroot` n’existe pas. Veuillez spécifier un chemin d’accès valide.

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056 : Xcode est introuvable dans l’emplacement par défaut (/ /applications/Xcode.app). Installez Xcode, ou passez un chemin d’accès personnalisé à l’aide de--sdkroot <path>.

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057 : Impossible de déterminer le chemin d’accès à Xcode.app à partir de la racine du sdk ' *'. Spécifiez le chemin d’accès complet à l’offre groupée Xcode.app.

Le chemin d’accès transmis à l’aide de `--sdkroot` ne spécifie pas une application de Xcode valide. Veuillez spécifier un chemin d’accès à une application de Xcode.

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058 : Le Xcode.app '\*' n’est pas valide (le fichier '\*' n’existe pas).

Le chemin d’accès transmis à l’aide de `--sdkroot` ne spécifie pas une application de Xcode valide. Veuillez spécifier un chemin d’accès à une application de Xcode.

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059 : Xcode actuellement sélectionné est introuvable sur le système : *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060 : Xcode actuellement sélectionné est introuvable sur le système. 'xcode-select--print-path' retourné ' *', mais ce répertoire n’existe pas.

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061 : Aucun Xcode.app spécifié (à l’aide de--sdkroot), en utilisant le système Xcode comme indiqué par '' xcode-select--print-path' : *

Il s’agit d’un avertissement d’information, en expliquant qui Xcode sera utilisé, dans la mesure où aucun n’a été spécifié.

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062 : Aucun Xcode.app spécifié (à l’aide de--sdkroot ou 'xcode-select--print-path'), à l’aide de la valeur par défaut Xcode à la place : *

Il s’agit d’un avertissement d’information, en expliquant qui Xcode sera utilisé, dans la mesure où aucun n’a été spécifié.

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063 : Impossible de trouver le fichier exécutable dans l’extension * (aucune entrée CFBundleExecutable dans son fichier Info.plist)

Chaque fichier Info.plist doit avoir un fichier exécutable (à l’aide de l’entrée CFBundleExecutable), mais une entrée doit être générée automatiquement lors de la génération.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un cas de test.

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064 : Xamarin.iOS prend uniquement en charge les frameworks incorporés avec les projets unifiée.

Xamarin.iOS ne prend en charge des infrastructures embedded lors de l’utilisation de l’API unifiée ; Mettez à jour votre projet pour utiliser l’API unifiée.

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065 : Xamarin.iOS ne prend en charge frameworks incorporés lors de la cible de déploiement est au moins 8.0 (cible de déploiement actuelle : * incorporé frameworks : *)

Xamarin.iOS prend uniquement en charge les frameworks incorporés lorsque la cible de déploiement est au moins 8.0 (étant donné que les versions antérieures d’e/s ne prend pas en charge les frameworks incorporés).

Mettez à jour la cible de déploiement dans le fichier Info.plist du projet à 8.0 ou version ultérieure.

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066 : Assembly de bureau d’enregistrement de build non valide : *

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un cas de test.

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067 : Bureau d’enregistrement non valide : *

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un cas de test.

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068 : Valeur non valide pour le framework cible : *.

Un framework cible non valide a été passé à l’aide de l’infrastructure cible argument--. Veuillez spécifier un framework cible valide.

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070 : Framework cible non valide : *. Frameworks cibles valides sont : *.

Un framework cible non valide a été passé à l’aide de l’infrastructure cible argument--. Veuillez spécifier un framework cible valide.

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071 : Plateforme inconnue : *. Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com avec un cas de test.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un cas de test.

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072 : Les extensions ne sont pas pris en charge pour la plateforme ' *'.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un cas de test.

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073 : Xamarin.iOS * ne prend pas en charge une cible de déploiement de * (la valeur minimale est *). Sélectionnez une cible de déploiement plus récente dans le fichier Info.plist de votre projet.

La cible de déploiement minimale est celle spécifiée dans le message d’erreur ; Sélectionnez une cible de déploiement plus récente dans le fichier Info.plist du projet.

Si la mise à jour de la cible de déploiement n’est pas possible, utilisez une ancienne version de Xamarin.iOS.

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074 : Xamarin.iOS * ne prend pas en charge une cible de déploiement minimal de * (la valeur maximale est *). Sélectionnez une cible de déploiement plus anciens dans le fichier Info.plist de votre projet ou mettre à niveau vers une version plus récente de Xamarin.iOS.

Xamarin.iOS ne prend pas en charge la définition de la cible de déploiement minimum vers une version supérieure à la version de pour que cette version particulière de Xamarin.iOS a été construite.

Sélectionnez une cible de déploiement minimum plus anciens dans le fichier Info.plist du projet, ou mettre à niveau vers une version plus récente de Xamarin.iOS.

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075 : Architecture non valide ' *' pour * projets. Les architectures valides sont : *

Une architecture non valide a été spécifiée. Vérifiez que l’architecture est valide.

<a name="MT0076" />

### <a name="mt0075-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0075 : Aucune architecture spécifiée (à l’aide de l’argument--abi). Une architecture est requise pour * projets.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un cas de test.

<a name="MT0077" />

### <a name="mt0076-watchos-projects-must-be-extensions"></a>MT0076 : Projets WatchOS doivent être des extensions.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un cas de test.

<a name="MT0078" />

### <a name="mt0077-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0077 : Les builds incrémentielles sont activées avec une cible de déploiement < 8.0 (actuellement *). Cela n’est pas pris en charge (l’application qui en résulte n’est pas lancée sur iOS 9), afin de la cible de déploiement est définie sur 8.0.

Il s’agit d’un avertissement informant que la cible de déploiement a été définie à 8.0 pour cette build afin que les générations incrémentielles fonctionnent correctement.

Les builds incrémentielles sont uniquement pris en charge la cible de déploiement est au moins 8.0 (étant donné que l’application qui en résulte n’est pas lancée sur iOS 9 dans le cas contraire).

<a name="MT0079" />

### <a name="mt0078-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0078 : La version de Xcode recommandée pour Xamarin.iOS * est Xcode * ou version ultérieure. La version actuelle de Xcode (trouvée dans *) est *.

Il s’agit d’un avertissement informant que la version actuelle de Xcode n’est pas la version de Xcode recommandée pour cette version de Xamarin.iOS.

Mettez à niveau Xcode pour garantir un comportement optimal.

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080 : La désactivation de NewRefCount,--new-refcount:false, est déconseillée.

Il s’agit d’un avertissement indiquant que la demande pour désactiver le nouveau refcount (--new - refcount:false) a été ignoré.

La nouvelle fonctionnalité refcount est maintenant obligatoire pour tous les projets, et il n’est donc pas possible de désactiver plus.

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081 : L’argument de ligne de commande--rapport d’incidents téléchargement également nécessite--téléchargement-incident-rapports pour.

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082 : REPL (--enable-repl) est uniquement pris en charge lors de la liaison n’est pas utilisée (--nolink).

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083 : Bitcode ASM seule n’est pas pris en charge sur watchOS. Utilisez soit bitcode-- : marqueur ou--bitcode : complète.

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084 : Bitcode n’est pas pris en charge dans le simulateur. Ne passez pas--bitcode lors de la génération pour le simulateur.

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085 : Aucune référence à ' *' a été trouvé. Il sera ajouté automatiquement.

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086 : Un framework cible (---framework cible) doit être spécifié lors de la génération pour TVOS ou WatchOS.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un cas de test.

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087 : Les builds incrémentielles (--fastdev) n’est pas pris en charge avec le GC Boehm. Les builds incrémentielles sont désactivés.

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088 : Le garbage collector doit être en mode coopératif pour applications watchOS. Supprimez le coop : false argument--à mtouch.

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089 : L’option '\*« ne peut pas de prendre la valeur »\*» lorsque le mode coopératif est activé pour le garbage collector.

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091 : Cette version de Xamarin.iOS exige que le * Kit de développement logiciel (livré avec Xcode *). Soit mise à niveau de Xcode pour obtenir les fichiers d’en-tête requis ou de définir le comportement de l’éditeur de liens géré pour lier les SDK Framework uniquement (à essayer d’éviter les nouvelles API).

Xamarin.iOS nécessite les fichiers d’en-tête, à partir de la version SDK spécifié dans le message d’erreur, pour générer votre application. La méthode recommandée pour corriger cette erreur consiste à mettre à niveau de Xcode pour obtenir le Kit de développement logiciel requis, cela inclut tous les fichiers d’en-tête requis. Si vous disposez de plusieurs versions de Xcode installée, ou que vous souhaitez utiliser un Xcode dans un emplacement non défini par défaut, veillez à définir l’emplacement de Xcode correct dans les préférences de l’IDE.

Un risque potentiel, autre solution consiste à activer l’éditeur de liens géré. Cela supprimera inutilisé API, y compris, dans la plupart des cas, la nouvelle API où les fichiers d’en-tête sont manquantes (ou incomplets). Cela ne fonctionne pas si votre projet utilise des API qui a été introduit dans un kit SDK plus récent que celui votre Xcode fournit toutefois.

Une solution de dernière-paille consisterait à utiliser une ancienne version de Xamarin.iOS, qui prend en charge le SDK de votre projet requiert.

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093 : 'Mlaunch' est introuvable.

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095 : Les fichiers de compilation AOT n’a pas pu être copiées vers le répertoire de destination {dest} : {error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096 : Aucune référence à Xamarin.iOS.dll a été trouvé.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099 : Erreur interne *. Veuillez soumettre un rapport de bogue avec un cas de test (http://bugzilla.xamarin.com).

Ce message d’erreur est signalé en cas d’échec d’une vérification de cohérence interne dans Xamarin.iOS.

Cela indique un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un cas de test.

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100 : Cible de génération assembly non valide : ' *'. Veuillez soumettre un rapport de bogue avec un cas de test (http://bugzilla.xamarin.com).

Ce message d’erreur est signalé en cas d’échec d’une vérification de cohérence interne dans Xamarin.iOS.

Il s’agit toujours d’un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un cas de test.

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101 : L’assembly ' *' est spécifié plusieurs fois dans--les arguments de l’assembly cible de génération.

L’assembly mentionné dans le message d’erreur est spécifié plusieurs fois dans--les arguments de l’assembly cible de génération. Vérifiez que chaque assembly est mentionné seulement une seule fois.

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102 : Les assemblys\*'et'\*' portent le même nom de cible ('\*»), mais différentes cibles ('\*' et ' *').

Les assemblys mentionnés dans le message d’erreur ont des cibles de génération en conflit.

Par exemple :

    --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary

Cet exemple tente de créer une bibliothèque dynamique et une infrastructure avec la même marque (`MyBinary`).

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103 : L’objet statique '\*» contient plus d’un assembly ('\*'), mais chaque objet statique doit correspondre à un seul assembly.

Les assemblys mentionnés dans le message d’erreur sont compilés à un seul objet statique. Cela n’est pas autorisé, tous les assemblys doivent être compilées dans un autre objet statique.

Par exemple :

    --assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary

Cet exemple tente de créer un objet statique (`MyBinary`) composé de deux assemblys (`Assembly1.dll` et `Assembly2.dll`), qui n’est pas autorisé.

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105 : Aucune cible de génération d’assembly a été spécifié pour ' *'.

Lorsque l’assembly de spécification de build à l’aide de la cible `--assembly-build-target`, chaque assembly dans l’application doit avoir une cible de génération affectée.

Cette erreur est signalée lorsque l’assembly mentionné dans le message d’erreur n’a pas un assembly cible affecté de génération.

Consultez la documentation `--assembly-build-target` pour plus d’informations.

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106 : Nom de la cible de build de l’assembly '\*' n’est pas valide : le caractère '\*' n’est pas autorisée.

Le nom de cible de build assembly doit être un nom de fichier valide.

Par exemple, ces valeurs déclenche cette erreur :

    --assembly-build-target:Assembly1.dll=staticobject=my/path.o

Étant donné que `my/path.o` n’est pas un nom de fichier valide en raison du caractère de séparation de répertoire.

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107 : Les assemblys\*' ont différentes optimisations LLVM personnalisées (\*), qui n’est pas autorisé quand elles sont compilées à un binaire unique.

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108 : Cible de génération de l’assembly ' *' ne correspond pas à tous les assemblys.

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109 : L’assembly '{0}' a été chargé à partir d’un autre chemin d’accès que le chemin d’accès fourni (fourni le chemin d’accès : {1}, chemin d’accès réel : {2}).

Il s’agit d’un avertissement indiquant qu’un assembly référencé par l’application a été chargé à partir d’un autre emplacement demandé.

Cela peut signifier que l’application fait référence à plusieurs assemblys portant le même nom mais provenant de différents emplacements, ce qui peuvent produire des résultats inattendus (uniquement le premier assembly sera utilisé).

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110 : Les builds incrémentielles ont été désactivés, car cette version de Xamarin.iOS ne prend pas en charge les générations incrémentielles dans les projets qui incluent des bibliothèques de liaison de tiers et qui est compilé en bitcode.

Les builds incrémentielles ont été désactivés, car cette version de Xamarin.iOS ne prend pas en charge les générations incrémentielles dans les projets qui incluent des bibliothèques de liaison de tiers et qui est compilé en bitcode (projets tvOS et watchOS).

Aucune action n’est requise de votre part, ce message est purement informatif.

Pour plus d’informations, consultez de bogue[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Cet avertissement n’est pas plus signalé.

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111 : Bitcode a été activée, car cette version de Xamarin.iOS ne prend pas en charge watchOS de génération des projets à l’aide de LLVM sans activer bitcode.

Bitcode a été activée automatiquement, car cette version de Xamarin.iOS ne prend pas en charge watchOS de génération des projets à l’aide de LLVM sans activer bitcode.

Aucune action n’est requise de votre part, ce message est purement informatif.

Pour plus d’informations, consultez de bogue[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112 : Partage de code natif a été désactivée, car *

Il existe plusieurs raisons partage de code peut être désactivé :

* Étant donné que la cible de déploiement de l’application conteneur est antérieure à iOS 8.0 (il a *)).

Partage de code natif nécessite iOS 8.0, car le partage de code natif est implémenté à l’aide de frameworks de l’utilisateur, qui a été introduite avec iOS 8.0.

* Étant donné que l’application de conteneur inclut les assemblys I18N (*).

Partage de code natif est actuellement pas pris en charge si l’application de conteneur inclut les assemblys I18N.

* Étant donné que l’application de conteneur a des définitions xml personnalisé pour l’éditeur de liens managé (*).

Le partage de code natif nécessite n’est pas pris en charge pour les projets qui utilisent des définitions xml personnalisé pour l’éditeur de liens géré.

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113 : Partage de code natif a été désactivé pour l’extension ' *', car *.

* Étant donné que les options de bitcode diffèrent entre l’application de conteneur (\*) et l’extension (\*).

  Le partage de code natif nécessite que les options de bitcode correspondent entre les projets qui partagent du code.

* Étant donné que--cible de génération d’assembly options diffèrent de l’application de conteneur (\*) et l’extension (\*).

  Partage de code natif requiert que--cible de génération d’assembly options sont identiques entre les projets qui partagent du code.

  Cette condition peut se produire si les builds incrémentielles sont pas activé ou désactivé dans tous les projets.

* Étant donné que les assemblys I18N diffèrent entre l’application de conteneur (\*) et l’extension (\*).

  Partage de code natif n’est actuellement pas pris en charge pour les extensions qui incluent les assemblys I18N.

* Étant donné que les arguments pour le compilateur AOT diffèrent entre l’application de conteneur (\*) et l’extension (\*).

  Partage de code natif requiert que les arguments pour le compilateur AOT ne diffèrent pas entre les projets qui partagent du code.

* Étant donné que les autres arguments pour le compilateur AOT diffèrent entre l’application de conteneur (\*) et l’extension (\*).

  Partage de code natif requiert que les arguments pour le compilateur AOT ne diffèrent pas entre les projets qui partagent du code.

  Cette condition se produit si « Effectuer toutes les opérations de virgule flottante 32 bits en tant que valeur float 64 bits » diffère entre projets.

* Étant donné que LLVM n’est pas activé ou désactivé dans l’application conteneur (\*) et l’extension (\*).

  Le partage de code natif nécessite que LLVM est activé ou désactivé pour tous les projets qui partagent du code.

* Étant donné que les paramètres de l’éditeur de liens managé sont différents entre l’application de conteneur (\*) et l’extension (\*).

  Partage de code natif requiert que les paramètres de l’éditeur de liens gérés sont identiques pour tous les projets qui partagent du code.

* Étant donné que les assemblys ignorées pour l’éditeur de liens managé sont différents entre l’application de conteneur (\*) et l’extension (\*).

  Partage de code natif requiert que les paramètres de l’éditeur de liens gérés sont identiques pour tous les projets qui partagent du code.

* Étant donné que l’extension a des définitions xml personnalisé pour l’éditeur de liens managé (*).

  Le partage de code natif nécessite n’est pas pris en charge pour les projets qui utilisent des définitions xml personnalisé pour l’éditeur de liens géré.

* Étant donné que l’application de conteneur ne génère pas de l’ABI * (pendant l’extension de construction pour cette ABI).

  Le partage de code natif nécessite que l’application de conteneur généré pour toutes les architectures n’importe quelle extension de l’application s’appuie pour.

  Par exemple : cette condition se produit lorsqu’une extension s’appuie pour ARM64 + ARMv7, mais l’application de conteneur génère uniquement pour ARM64.

* Étant donné que la création de l’application conteneur pour l’ABI \*, qui n’est pas compatible avec ABI de l’extension (\*).

  Le partage de code natif nécessite que tous les projets de build pour exactement la même API.

  Par exemple : cette condition se produit lorsqu’une extension s’appuie pour ARMv7 + llvm + thumb2, mais l’application de conteneur ne génère qu’ARMv7 + llvm.

* Étant donné que l’application de conteneur fait référence à l’assembly '\*'from'\*', tandis que l’extension fait référence à une version différente de ' *'.

  Le partage de code natif nécessite que tous les projets qui partagent du code utilisent les mêmes versions pour tous les assemblys.

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115 : Il est recommandé pour référencer des symboles dynamiques à l’aide de code (--symbole-en mode dynamique = code) lorsque le bitcode est activé.

Projets Xamarin.iOS fait souvent référence symboles natifs dynamiquement, ce qui signifie que l’éditeur de liens natif peut supprimer des symboles natifs pendant le processus de liaison natif, étant donné que l’éditeur de liens natif ne voit pas que ces symboles sont utilisés.

Généralement Xamarin.iOS demandera à l’éditeur de liens natif pour conserver des symboles (à l’aide de la `-u symbol` indicateur de l’éditeur de liens), mais lorsque compilation pour bitcode l’éditeur de liens natif n’accepte pas la `-u` indicateur.

Xamarin.iOS a implémenté une solution alternative : nous générer du code natif supplémentaire qui fait référence à ces symboles, et par conséquent, l’éditeur de liens natif s’affiche que ces symboles sont utilisés. Cela est fait automatiquement lors de la compilation à bitcode.

Si `--dynamic-symbol-mode=linker` est passé à mtouch, ce remplacement solution va être désactivée, et tente de passer par Xamarin.iOS `-u` pour l’éditeur de liens natif. Cela entraîne probablement des erreurs de l’éditeur de liens natif.

La solution consiste à supprimer le `--dynamic-symbol-mode=linker` argument à partir des arguments mtouch supplémentaires dans les options de génération du projet.

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116 : Architecture non valide : {arch}. les architectures 32 bits ne sont pas prises en charge lors de la cible de déploiement est 11 ou version ultérieure. Assurez-vous que le projet ne génère pas d’une architecture 32 bits.

iOS 11 ne contient pas de prise en charge pour les applications 32 bits, donc il n’est pas pris en charge pour générer pour une application 32 bits lorsque la cible de déploiement est iOS 11 ou version ultérieure.

Modifier l’architecture cible dans les options de génération du projet iOS pour arm64, soit modifier la cible de déploiement dans le fichier Info.plist du projet vers une version d’iOS antérieure.

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117 : Impossible de lancer une application 32 bits sur un simulateur prend uniquement en charge 64 bits.

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118 : Les fichiers de compilation AOT est introuvable dans le répertoire attendu '{msymdir}'.

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123 : L’assembly exécutable * ne fait pas référence *.

Aucune référence n’a été trouvée à l’assembly de plateforme (Xamarin.iOS.dll / Xamarin.TVOS.dll / Xamarin.WatchOS.dll) dans l’assembly exécutable.

Cela se produit généralement lorsqu’il n’existe pas de code dans le projet exécutable qui utilise quoi que ce soit à partir de l’assembly de plateforme ; par exemple une méthode Main vide (et aucun autre code) affiche cette erreur :

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

Pour résoudre l’erreur, à l’aide d’une API à partir de l’assembly de plateforme :

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124 : Impossible de définir la langue actuelle sur « {lang} » (en fonction de LANG = {LANG}) : {exception}

Il s’agit d’un avertissement indiquant que le langage actuel n’a pas pu être défini pour le langage dans le message d’erreur.

Le langage actuel par défaut pour la langue du système.

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125 : --Cible de génération d’assembly argument de ligne de commande est ignoré dans le simulateur.

Aucune action n’est requise, ce message est purement informatif.

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126 : Les builds incrémentielles ont été désactivés, car les builds incrémentielles ne sont pas pris en charge dans le simulateur.

Aucune action n’est requise, ce message est purement informatif.

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127 : Les builds incrémentielles ont été désactivés, car cette version de Xamarin.iOS ne prend pas en charge les générations incrémentielles dans les projets qui incluent des bibliothèques de liaison de plus d’un tiers.

Les builds incrémentielles ont été désactivés automatiquement, car cette version de Xamarin.iOS ne pas toujours générer des projets avec plusieurs bibliothèques de liaison de tiers correctement.

Aucune action n’est requise, ce message est purement informatif.

Pour plus d’informations, consultez de bogue[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128 : Peut toucher pas le fichier ' *': *

Une défaillance s’est produite lors de la toucher un fichier (ce qui est effectué pour garantir des builds partielles sont effectuées correctement).

Cet avertissement peut probablement être ignoré ; en cas de problème signaler un bogue (https://bugzilla.xamarin.com ] (https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)) et il est examiné.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx : Messages d’erreur associés du projet

### <a name="mt10xx-installer--mtouch"></a>MT10xx : Programme d’installation / mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001 : Impossible de trouver une application dans le répertoire spécifié

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002 : Impossible de créer le liens symboliques, les fichiers ont été copiés.

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003 : Impossible d’interrompre l’application ' *'. Vous devrez peut-être arrêter l’application manuellement.

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004 : Impossible d’obtenir la liste des applications installées.

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005 : Impossible d’interrompre l’application '\*'sur l’appareil'\*' : *-il se peut que vous deviez arrêter l’application manuellement.

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006 : Ne peut pas installer l’application '\*'sur l’appareil'\*' : *.

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007 : Échec du lancement de l’application '\*'sur l’appareil'\*' : *. Vous pouvez toujours lancer l’application manuellement en appuyant dessus.

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008 : Impossible de lancer le simulateur

Cette erreur est signalée si mtouch n’a pas pu lancer le simulateur.   Cela peut arriver parfois qu’il existe déjà un processus de simulateur obsolètes ou morts en cours d’exécution.

La commande suivante est émise sur la ligne de commande Unix peut être utilisée pour supprimer un processus de simulateur bloqué :

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009 : Impossible de copier l’assembly '\*'en'\*' : *

Il s’agit d’un problème connu dans certaines versions de Xamarin.iOS.

Si cela se produit pour vous, essayez la solution de contournement suivante :

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

Toutefois, étant donné que ce problème a été résolu dans la dernière version de Xamarin.iOS, veuillez soumettre un bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec vos informations de version complète et de la sortie de journal de génération.

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010 : Impossible de charger l’assembly ' *': *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011 : Pas pu ajouter le fichier de ressources manquant : ' *'

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012 : Impossible de répertorier les applications sur l’appareil ' *': *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013 : Erreur de suivi des dépendances : aucun fichier à comparer. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com avec un cas de test.

Cela indique un bogue dans Xamarin.iOS. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un caes de test.

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014 : Impossible d’utiliser une version mise en cache de ' *': *.

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015 : Impossible de créer l’exécutable ' *': *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015 : Impossible de créer l’exécutable ' *': *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016 : Échec de création du fichier mentions, car il existe déjà un répertoire portant le même nom.

Supprimer le répertoire `NOTICE` à partir du projet.

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017 : Impossible de créer le fichier mentions : *.

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018 : Échec de la vérification de signature de code de votre application et n’a pas pu être installée sur l’appareil ' *'. Vérifiez vos certificats, profils, de provisionnement et regrouper des identificateurs. Probablement votre appareil n’est pas dans le profil de configuration sélectionné (erreur : 0xe8008015).

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019 : Votre application possède les droits non pris en charge par votre profil de provisionnement en cours et n’a pas pu être installée sur l’appareil ' *'. Vérifiez le journal de l’appareil iOS pour des informations détaillées (erreur : 0xe8008016).

Cela peut se produire si :

* Votre application dispose de droits que le profil d’approvisionnement actuel ne prend pas en charge.
  Solutions possibles :
  - Spécifiez un autre profil de provisionnement qui prend en charge les droits nécessaires à votre application.
  - Supprimer les droits non pris en charge dans le profil de provisionnement en cours.
* L’appareil que vous tentez de déployer sur n’est ne pas inclus dans le profil de configuration que vous utilisez.
  Solutions possibles :
  - Créer une application à partir d’un modèle dans Xcode et sélectionnez le profil d’approvisionnement même déployer vers le même appareil. Parfois Xcode peut automatiquement actualiser des profils de provisionnement avec les nouveaux appareils (dans d’autres cas que Xcode vous demandera quoi faire).
  -Consultez le centre de développement iOS et mettre à jour le profil de provisionnement avec le nouvel appareil, puis téléchargez le profil de configuration mis à jour sur votre ordinateur.

Dans la plupart des cas que plus d’informations sur l’échec seront imprimés dans le journal de l’appareil iOS, qui peut aider à diagnostiquer le problème.

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020 : Échec du lancement de l’application '\*'sur l’appareil'\*' : *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021 : Le fichier n’a pas pu être copier '\*'en'\*' : {2}

Un fichier n’a pas pu être copié. Le message d’erreur à partir de l’opération de copie a plus d’informations sur l’erreur.

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022 : Impossible de copier le répertoire '\*'en'\*' : {2}

Un répertoire n’a pas pu être copié. Le message d’erreur à partir de l’opération de copie a plus d’informations sur l’erreur.

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023 : Ne peut pas communiquer avec l’appareil pour trouver l’application ' *' : *

Une erreur s’est produite lors de la tentative de recherche d’une application sur l’appareil.

Solutions possibles résoudre ce problème :

* Supprimer l’application à partir de l’appareil et réessayez.
* Déconnectez l’appareil et le reconnecter.
* Redémarrer l’appareil.
* Redémarrez l’ordinateur Mac.

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024 : Signature de l’application n’a pas pu être vérifiée sur le périphérique ' *'. Vérifiez que le profil de provisionnement est installé et n’a pas expiré (erreur : 0xe8008017).

L’appareil a rejeté l’installation de l’application, car la signature n’a pas pu être vérifiée.

Vérifiez que le profil de provisionnement est installé et n’a pas expiré.

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025 : Impossible de lister les rapports d’incident sur l’appareil *.

Une erreur s’est produite lors de la tentative répertorier les rapports d’incident sur l’appareil.

Solutions possibles résoudre ce problème :

* Supprimer l’application à partir de l’appareil et réessayez.
* Déconnectez l’appareil et le reconnecter.
* Redémarrer l’appareil.
* Redémarrez l’ordinateur Mac.
* Synchroniser l’appareil avec iTunes (Cela supprimera les rapports d’incidents à partir de l’appareil).

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026 : Le rapport d’incident n’a pas pu être télécharger * à partir de l’appareil *.

Une erreur s’est produite lorsque vous tentez de télécharger les rapports d’incident à partir de l’appareil.

Solutions possibles résoudre ce problème :

* Supprimer l’application à partir de l’appareil et réessayez.
* Déconnectez l’appareil et le reconnecter.
* Redémarrer l’appareil.
* Redémarrez l’ordinateur Mac.
* Synchroniser l’appareil avec iTunes (Cela supprimera les rapports d’incidents à partir de l’appareil).

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027 : Ne peut pas utiliser Xcode 7 + et pour lancer des applications sur les appareils iOS * (Xcode 7 prend uniquement en charge iOS 6 +).

Il n’est pas possible d’utiliser Xcode 7 + et pour lancer des applications sur les appareils avec la version iOS inférieure à 6.0.

Veuillez utiliser une version antérieure de Xcode, ou appuyez sur l’application manuellement pour le lancer.

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028 : Spécification de périphérique non valide : ' *'. Attendu 'ios', 'watchos' ou 'all'.

La spécification de périphérique passé en utilisant--appareil n’est pas valide. Les valeurs valides sont : « ios', 'watchos' ou 'all'.

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029 : Impossible de trouver une application dans le répertoire spécifié : *

Le chemin d’accès de l’application passé à--launchdev n’existe pas. Veuillez spécifier un ensemble d’applications valide.

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030 : Lancement d’applications sur l’appareil à l’aide d’un identificateur de bundle est déconseillé. Veuillez transmettre le chemin d’accès complet à l’application à lancer.

Il est recommandé de transmettre le chemin d’accès à l’application à lancer sur un appareil au lieu de simplement l’id de bundle.

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031 : Ne peut pas lancer l’application '\*'sur l’appareil'\*', car l’appareil est verrouillé. Déverrouiller l’appareil, puis réessayez.

Déverrouiller l’appareil, puis réessayez.

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032 : Cet exécutable de l’application peut être trop volumineux (* Mo) à exécuter sur l’appareil. Si l’activation de bitcode vous souhaiterez peut-être désactiver pour le développement, il est uniquement nécessaire à soumettre leurs applications à Apple.

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033 : Impossible de désinstaller l’application '\*'à partir de l’appareil'\*' : *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035 : Ne peut pas inclure les différentes versions du framework « {name} »

Il n’est pas possible de lier avec différentes versions de la même infrastructure.

Cela se produit généralement lorsqu’une extension fait référence à une version différente d’une infrastructure à l’application de conteneur (éventuellement par l’intermédiaire d’un assembly de la liaison de tiers).

Ce message d’erreur Il y aura plusieurs [MT1036](#MT1036) erreurs répertoriant les chemins d’accès pour chaque framework différents.

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036 : Le Framework « {name} » inclus à partir de : {path} (associé à l’erreur précédente)

Cette erreur est signalée uniquement avec [MT1036](#MT1036). Consultez [MT1036](#MT1036) pour plus d’informations.

### <a name="mt11xx-debug-service"></a>MT11xx : Déboguer le Service

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101 : Application n’a pas pu être démarrer

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102 : A pas pu attacher à l’application (pour l’arrêtiez) : *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103 : Impossible de détacher

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104 : Échec d’envoi de paquets : *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105 : Type de réponse inattendu

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106 : Impossible d’obtenir la liste des applications sur l’appareil : Demande a expiré.

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107 : Échec du lancement de l’application : *

Vérifiez si votre appareil est verrouillé.

Si vous déployez une application d’entreprise ou à l’aide d’un profil d’approvisionnement, vous devrez peut-être faire confiance au développeur (cela est expliqué <a href="https://stackoverflow.com/a/30726375/183422">ici</a>).

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108 : Outils de développement introuvable pour cet appareil XX (aa).

Quelques opérations à partir de mtouch nécessitent le <tt>DeveloperDiskImage.dmg</tt> fichier qui doit être présent.   Ce fichier fait partie de Xcode et se trouve généralement par rapport au Kit de développement logiciel que vous utilisez pour la build, dans le <tt>Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg</tt>.

Cette erreur peut se produire soit parce que vous n’avez pas un DeveloperDiskImage.dmg qui correspond à l’appareil que vous vous êtes connecté.

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109 : Échec de l’application lancer, car l’appareil est verrouillé. Déverrouiller l’appareil, puis réessayez.

Vérifiez si votre appareil est verrouillé.

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110 : Lancement en raison des restrictions de sécurité iOS de l’application a échoué. Vérifiez que le développeur est approuvé.

Si vous déployez une application d’entreprise ou à l’aide d’un profil d’approvisionnement, vous devrez peut-être faire confiance au développeur (cela est expliqué <a href="https://stackoverflow.com/a/30726375/183422">ici</a>).

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111 : Application lancée avec succès, mais il n’est pas possible d’attendre que l’application quitter comme demandé, car il n’est pas possible de détecter l’arrêt de l’application lors du lancement à l’aide de gdbserver.

### <a name="mt12xx-simulator"></a>MT12xx : Simulateur

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201 : N’a pas pu charger le simulateur : *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202 : Configuration de simulateur non valide : *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203 : Spécification de simulateur non valide : *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204 : Spécification de simulateur non valide ' *': runtime non spécifiée.

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205 : Spécification de simulateur non valide ' *': type d’appareil non spécifié.

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206 : N’a pas trouvé le runtime de simulateur ' *'.

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207 : Impossible de trouver le type d’appareil simulateur ' *'.

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208 : N’a pas trouvé le runtime de simulateur ' *'.

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209 : Impossible de trouver le type d’appareil simulateur ' *'.

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210: Spécification de simulateur non valide : \*, clé inconnue '\*'

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211 : La version de simulateur '\*'ne prend pas en charge le type de simulateur'\*'

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212 : Impossible de créer une version de simulateur où type = * runtime et = *.

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213 : Spécification de simulateur non valide pour Xcode 4 : *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214 : Spécification de simulateur non valide pour Xcode 5 : *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215 : Kit de développement logiciel non valide spécifié : *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216 : N’a pas trouvé le simulateur UDID ' *'.

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217 : Le bundle d’applications n’a pas pu être charger ' *'.

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218 : Aucun identificateur de bundle ne trouvé dans l’application au ' *'.

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219 : N’a pas trouvé le simulateur pour ' *'.

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220 : La dernière exécution du simulateur est introuvable pour l’appareil ' *'.

Cela indique généralement un problème avec Xcode.

Solutions possibles résoudre ce problème :

* Utilisez le simulateur une fois dans Xcode.
* Passer d’une version SDK explicite à l’aide du Kit de développement logiciel-- <version>.
* Réinstallez Xcode.

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221 : Le simulateur iPhone apparié introuvable pour le simulateur WatchOS ' *'.

Lors du lancement d’une application WatchOS dans un simulateur WatchOS, il doit y avoir un jumelée iOS Simulator également.

Regardez les simulateurs peuvent être jumelés avec iOS simulateurs à l’aide de l’interface utilisateur des appareils de Xcode (menu Fenêtre -> appareils).

### <a name="mt13xx-linkwith"></a>MT13xx : [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301 : Bibliothèque native `*` (\*) a été ignoré, car il ne correspond pas à l’architecture(s) de build actuel (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302 : La bibliothèque native n’a pas pu être extraire ' *' à partir de '+'. Vérifiez que la bibliothèque native a été correctement incorporée dans l’assembly managé (si l’assembly a été généré à l’aide d’un projet de liaison, la bibliothèque native doit être incluse dans le projet et son Action de génération doit être 'ObjcBindingNativeLibrary').

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303 : Impossible de décompresser le framework natif '\*'from'\*'. Passez en revue le journal de génération pour plus d’informations à partir de la commande native « zip ».

N’a pas pu décompresser le framework natif spécifié à partir de la bibliothèque de liaison.

Passez en revue le journal bulid pour plus d’informations sur cet incident à partir de la commande native « zip ».

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304 : Le framework incorporé « * » dans * n’est pas valide : il ne contient pas d’un fichier Info.plist.

Le framework incorporé spécifié ne contient-elle pas d’un fichier Info.plist et est donc pas un framework valid.

Vérifiez que le framework est valid.

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305 : La bibliothèque de liaison '\*' contient une infrastructure de l’utilisateur (\*), mais utilisateur incorporées infrastructures nécessitent iOS 8.0 (la cible de déploiement actuelle est *). Définissez la cible de déploiement dans le fichier Info.plist au moins 8.0.

La bibliothèque de liaison spécifié contient une structure incorporée, mais Xamarin.iOS prend uniquement en charge des infrastructures embedded sur iOS version 8.0 ou ultérieure.

Définissez la cible de déploiement dans le fichier Info.plist au moins 8.0 pour résoudre cette erreur (ou n’utilisez pas frameworks incorporés).

### <a name="mt14xx-crash-reports"></a>MT14xx : Rapports d’incidents

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400 : Impossible d’ouvrir le service de rapport d’incident : AFCConnectionOpen retournée *

Une erreur s’est produite lorsque vous tentez d’accéder aux rapports d’incidents à partir de l’appareil.

Solutions possibles résoudre ce problème :

* Supprimer l’application à partir de l’appareil et réessayez.
* Déconnectez l’appareil et le reconnecter.
* Redémarrer l’appareil.
* Redémarrez l’ordinateur Mac.
* Synchroniser l’appareil avec iTunes (Cela supprimera les rapports d’incidents à partir de l’appareil).

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401 : Impossible de fermer le service de rapport d’incident : AFCConnectionClose retournée *

Une erreur s’est produite lorsque vous tentez d’accéder aux rapports d’incidents à partir de l’appareil.

Solutions possibles résoudre ce problème :

* Supprimer l’application à partir de l’appareil et réessayez.
* Déconnectez l’appareil et le reconnecter.
* Redémarrer l’appareil.
* Redémarrez l’ordinateur Mac.
* Synchroniser l’appareil avec iTunes (Cela supprimera les rapports d’incidents à partir de l’appareil).

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402 : Info de fichier n’a pas pu être lu * : AFCFileInfoOpen retournée *

Une erreur s’est produite lorsque vous tentez d’accéder aux rapports d’incidents à partir de l’appareil.

Solutions possibles résoudre ce problème :

* Supprimer l’application à partir de l’appareil et réessayez.
* Déconnectez l’appareil et le reconnecter.
* Redémarrer l’appareil.
* Redémarrez l’ordinateur Mac.
* Synchroniser l’appareil avec iTunes (Cela supprimera les rapports d’incidents à partir de l’appareil).

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403 : Impossible de lire le rapport d’incidents : AFCDirectoryOpen (*) retourné : *

Une erreur s’est produite lorsque vous tentez d’accéder aux rapports d’incidents à partir de l’appareil.

Solutions possibles résoudre ce problème :

* Supprimer l’application à partir de l’appareil et réessayez.
* Déconnectez l’appareil et le reconnecter.
* Redémarrer l’appareil.
* Redémarrez l’ordinateur Mac.
* Synchroniser l’appareil avec iTunes (Cela supprimera les rapports d’incidents à partir de l’appareil).

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404 : Impossible de lire le rapport d’incidents : AFCFileRefOpen (*) retourné : *

Une erreur s’est produite lorsque vous tentez d’accéder aux rapports d’incidents à partir de l’appareil.

Solutions possibles résoudre ce problème :

* Supprimer l’application à partir de l’appareil et réessayez.
* Déconnectez l’appareil et le reconnecter.
* Redémarrer l’appareil.
* Redémarrez l’ordinateur Mac.
* Synchroniser l’appareil avec iTunes (Cela supprimera les rapports d’incidents à partir de l’appareil).

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405 : Impossible de lire le rapport d’incidents : AFCFileRefRead (*) retourné : *

Une erreur s’est produite lorsque vous tentez d’accéder aux rapports d’incidents à partir de l’appareil.

Solutions possibles résoudre ce problème :

* Supprimer l’application à partir de l’appareil et réessayez.
* Déconnectez l’appareil et le reconnecter.
* Redémarrer l’appareil.
* Redémarrez l’ordinateur Mac.
* Synchroniser l’appareil avec iTunes (Cela supprimera les rapports d’incidents à partir de l’appareil).

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406 : Impossible de lister les rapports d’incidents : AFCDirectoryOpen (*) retourné : *

Une erreur s’est produite lorsque vous tentez d’accéder aux rapports d’incidents à partir de l’appareil.

Solutions possibles résoudre ce problème :

* Supprimer l’application à partir de l’appareil et réessayez.
* Déconnectez l’appareil et le reconnecter.
* Redémarrer l’appareil.
* Redémarrez l’ordinateur Mac.
* Synchroniser l’appareil avec iTunes (Cela supprimera les rapports d’incidents à partir de l’appareil).

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx : MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600 : Pas une Mach-O bibliothèque dynamique (en-tête inconnu « 0 x * ») : *.

Une erreur s’est produite lors du traitement de la bibliothèque dynamique en question.

Vérifiez que la bibliothèque dynamique est une bibliothèque dynamique Mach-O valide.

Le format d’une bibliothèque peut être vérifié à l’aide de la `file` commande à partir d’un terminal :

    file -arch all -l /path/to/library.dylib

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601 : Pas d’une bibliothèque statique (en-tête inconnu ' *') : *.

Une erreur s’est produite lors du traitement de la bibliothèque statique en question.

Vérifiez que la bibliothèque statique est une bibliothèque statique Mach-O valide.

Le format d’une bibliothèque peut être vérifié à l’aide de la `file` commande à partir d’un terminal :

    file -arch all -l /path/to/library.a

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602 : Pas une Mach-O bibliothèque dynamique (en-tête inconnu « 0 x * ») : *.

Une erreur s’est produite lors du traitement de la bibliothèque dynamique en question.

Vérifiez que la bibliothèque dynamique est une bibliothèque dynamique Mach-O valide.

Le format d’une bibliothèque peut être vérifié à l’aide de la `file` commande à partir d’un terminal :

    file -arch all -l /path/to/library.dylib

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603 : Format inconnu pour fat entrée à la position * dans *.

Une erreur s’est produite lors du traitement de l’archive fat en question.

Vérifiez que l’archive fat est valide.

Le format d’une archive fat peut être vérifié à l’aide de la `file` commande à partir d’un terminal :

    file -arch all -l /path/to/file

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604 : Fichier de type * n’est pas un fichier MachO (*).

Une erreur s’est produite lors du traitement du fichier MachO en question.

Vérifiez que le fichier est une bibliothèque dynamique Mach-O valide.

Le format d’un fichier peut être vérifié à l’aide de la `file` commande à partir d’un terminal :

    file -arch all -l /path/to/file

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx : Messages d’erreur de l’éditeur de liens

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001 : Impossible de lier les assemblys

Cette erreur signifie que l’éditeur de liens géré a rencontré une erreur inattendue, par exemple, une exception et ne peut pas terminer ou enregistrez l’assembly en cours de traitement. Plus d’informations sur l’erreur exacte fera partie du journal de génération, par exemple

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

Il est important envoyer un rapport de bogue pour ces problèmes. Dans la plupart des cas, une solution de contournement peut être fournie avant la publication d’un correctif approprié. Les informations ci-dessus sont critiques (ainsi que d’un cas de test et/ou le binairy assembly) pour résoudre le problème.

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002 : Impossible de résoudre la référence : *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003 : Option ' *' va être ignorée car la liaison est désactivée.

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004 : Fichier de définitions de l’éditeur de liens supplémentaires ' *' n’a pas été trouvé.

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005 : Définitions à partir de ' *' a pas pu être analysé.

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006 : Impossible de charger le fichier mscorlib.dll à partir de : *. Réinstallez Xamarin.iOS.

Cela indique généralement qu’il existe un problème avec votre installation de Xamarin.iOS. Essayez de réinstaller de Xamarin.iOS.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010 : HttpMessageHandler inconnu `*`. Les valeurs valides sont HttpClientHandler (valeur par défaut), CFNetworkHandler ou NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011 : TlsProvider inconnu `*`.  Les valeurs valides sont par défaut ou appletls.

La valeur donnée `tls-provider=` n’est pas un fournisseur TLS (Transport Layer Security) valide.

Le `default` et `appletls` sont les seules valeurs valides et les deux représentent la même option, qui consiste à fournir le SSL/TLS prend en charge à l’aide de l’API de TLS native Apple.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015 : HttpMessageHandler non valide `*` pour watchOS. La seule valeur valide est NSUrlSessionHandler.

Il s’agit d’un avertissement se produit parce que le fichier projet spécifie un HttpMessageHandler non valide.

Les versions antérieures de nos outils de l’aperçu généré par défaut une valeur non valide dans le fichier projet.

Pour résoudre cet avertissement, ouvrez le fichier de projet dans un éditeur de texte et supprimez tous les nœuds de HttpMessageHandler à partir du XML.

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016 : Non valide TlsProvider `legacy` option. La seule valeur valide `appletls` sera utilisé.

Le `legacy` fournisseur, qui a été un SSLv3 entièrement géré / TLSv1 seul fournisseur, n’est pas plus livré avec Xamarin.iOS. Les projets qui ont été à l’aide de ce fournisseur ancien et maintenant générer avec la plus récente `appletls` une.

Pour résoudre cet avertissement, ouvrez le fichier de projet dans un éditeur de texte et supprimer tous les ' MtouchTlsProvider'' nœuds à partir du XML.

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017 : N’a pas pu traiter description XML.

Cela signifie qu’une erreur sur le [fichier de configuration de l’éditeur de liens XML personnalisé](https://developer.xamarin.com/guides/cross-platform/advanced/custom_linking/) que vous avez fourni, veuillez consulter votre fichier.

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018 : L’assembly '\*' est référencé à partir de deux emplacements différents : '\*' et ' *'.

L’assembly mentionné dans le message d’erreur est chargé à partir de plusieurs emplacements. Veillez à toujours utiliser la même version d’un assembly.

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019 : Impossible de charger l’assembly racine ' *'

L’assembly racine n’a pas pu être chargé. Vérifiez que le chemin d’accès dans le message d’erreur fait référence à un fichier existant, et qu’il est d’un assembly .NET valide.

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x : Échec de la liaison optimiseur traitement `...`.

Une erreur inattendue s’est produite à optimiser généré le code de liaison. L’élément qui provoque le problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly nommé (ou qui contient le type ou une méthode nommée) vous devrez être fournie dans un [des bogues, rapport](http://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **mtouch supplémentaires arguments**).

Le dernier chiffre `x` sera :
* `0` pour un nom de l’assembly ;
* `1` nom du type ;
* `3` pour un nom de méthode ;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030 : Remove utilisateur basculement des ressources de traitement `...`.

Une erreur inattendue s’est produite lors de la tentative de suppression de ressources de l’utilisateur. L’assembly qui provoque le problème est nommé dans le message d’erreur. Pour résoudre ce problème à l’assembly sera doivent être fournis dans un [des bogues, rapport](http://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

Ressources de l’utilisateur sont des fichiers incluses dans les assemblys qui doivent être extraites au moment de la génération, pour créer le bundle d’applications (en tant que ressources). notamment :

* `__monotouch_content_*` et `__monotouch_pages_*` ressources ; et
* Bibliothèques natives incorporés à l’intérieur d’un assembly de liaison ;

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040 : Par défaut HttpMessageHandler setter échoué de traitement `...`.

Une erreur inattendue s’est produite lors de la tentative de définition de la valeur par défaut `HttpMessageHandler` pour l’application. Veuillez soumettre un [des bogues, rapport](http://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050 : Échec du traitement du code de programme de suppression `...`.

Une erreur inattendue s’est produite lors de la tentative de suppression du code à partir de la BCL livrée avec l’application. Veuillez soumettre un [des bogues, rapport](http://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060 : Échec du traitement du fermeture `...`.

Une erreur inattendue s’est produite lors de la tentative sceller des types ou méthodes (dernière) ou lorsque devirtualizing certaines méthodes. L’assembly qui provoque le problème est nommé dans le message d’erreur. Pour résoudre ce problème à l’assembly sera doivent être fournis dans un [des bogues, rapport](http://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070 : Échec du traitement du raccord de réduction de métadonnées `...`.

Une erreur inattendue s’est produite lors de la tentative réduire les métadonnées à partir de l’application. L’assembly qui provoque le problème est nommé dans le message d’erreur. Pour résoudre ce problème à l’assembly sera doivent être fournis dans un [des bogues, rapport](http://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080 : MarkNSObjects échoué de traitement `...`.

Une erreur inattendue s’est produite lors de la tentative de marquage `NSObject` sous-classes à partir de l’application. L’assembly qui provoque le problème est nommé dans le message d’erreur. Pour résoudre ce problème à l’assembly sera doivent être fournis dans un [des bogues, rapport](http://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090 : Échec du traitement du Incorporateur `...`.

Une erreur inattendue s’est produite lors du code inline à partir de l’application. L’assembly qui provoque le problème est nommé dans le message d’erreur. Pour résoudre ce problème à l’assembly devrez être fournie dans un [des bogues, rapport](https://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100 : Échec du traitement du Smart préserver de l’Enum Conversion `...`.

Une erreur inattendue s’est produite lors de la tentative marquer les méthodes de conversion pour les enums actives à partir de l’application. L’assembly qui provoque le problème est nommé dans le message d’erreur. Pour résoudre ce problème à l’assembly devrez être fournie dans un [des bogues, rapport](https://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101 : Impossible de résoudre la référence '\*', référencé à partir de la méthode'\*' dans ' *'.

Une référence d’assembly non valide a été rencontrée lors du traitement de la méthode mentionnée dans le message d’erreur.

L’assembly qui provoque le problème est nommé dans le message d’erreur. Pour résoudre ce problème à l’assembly sera doivent être fournis dans un [des bogues, rapport](https://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102 : Erreur lors du traitement de la méthode '\*'dans l’assembly'\*' : *

Une erreur inattendue s’est produite lors de la tentative de marquer la méthode mentionnée dans le message d’erreur.

L’assembly qui provoque le problème est nommé dans le message d’erreur. Pour résoudre ce problème à l’assembly sera doivent être fournis dans un [des bogues, rapport](https://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103 : Erreur lors du traitement d’assembly '\*' : *

Une erreur inattendue s’est produite lors du traitement d’un assembly.

L’assembly qui provoque le problème est nommé dans le message d’erreur. Pour résoudre ce problème à l’assembly devrez être fournie dans un [des bogues, rapport](https://bugzilla.xamarin.com) ainsi que d’un journal de génération complète avec le niveau de détail activée (par exemple, `-v -v -v -v` dans le **arguments mtouch supplémentaires**).

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104 : Impossible de lier l’assembly '{0}' car il est en mode mixte.

Les assemblys en mode mixte ne peuvent pas être traités par l’éditeur de liens.

Consultez https://msdn.microsoft.com/library/x0w2664k.aspx pour plus d’informations sur les assemblys en mode mixte.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx : Messages d’erreur de compilation AOT

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001 : Pu pas AOT l’assembly ' *'

Cela indique généralement un bogue dans le compilateur AOT. Veuillez soumettre un bogue [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) avec un projet qui peut être utilisé pour reproduire l’erreur.

Parfois, il est possible de contourner ce problème en désactivant les builds incrémentielles dans iOS du projet option de Build (mais il est toujours un bogue, par conséquent, veuillez le signaler quand même).

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-httpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbackshttpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbacks"></a>MT3002 : Restriction de AOT : Méthode ' *' doit être statique, car elle est décorée avec [MonoPInvokeCallback]. Consultez [https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks)

Ce message d’erreur provient le compilateur AOT.

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003 : Options de débogage et--llvm--conflictuelles. Soft-débogage est désactivé.

Le débogage n’est pas pris en charge lorsque la LLVM est activée. Si vous avez besoin déboguer l’application, désactivez d’abord LLVM.

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004 : Pu pas AOT l’assembly ' *', car il n’existe pas.

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005 : La dépendance '\*'de l’assembly'\*' est introuvable. Passez en revue les références du projet.

Cela se produit généralement lorsqu’un assembly fait référence à une autre version d’un assembly de plateforme (généralement la version .NET 4 du fichier mscorlib.dll).

Cela n’est pas pris en charge et peut générer ou s’exécuter correctement (l’assembly peut utiliser API à partir de la version de .NET 4 du fichier mscorlib.dll la version de Xamarin.iOS ne possédant pas).

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006 : Impossible de calculer une carte complète des dépendances pour le projet. Ainsi, les durées de génération plus lentes étant donné que Xamarin.iOS ne peut pas détecter correctement ce qui doit être reconstruit (et ce qui ne doivent pas être reconstruits). Passez en revue les avertissements précédentes pour plus d’informations.

 créer ou de s’exécuter correctement (l’assembly peut utiliser API à partir de la version de .NET 4 du fichier mscorlib.dll la version de Xamarin.iOS ne possédant pas).

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007 : Les informations de débogage (*.mdb) de fichiers ne seront pas chargés lorsque llvm est activé.

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008 : Prise en charge Bitcode requiert l’utilisation du serveur principal de LLVM AOT (--llvm)

Prise en charge Bitcode requiert l’utilisation du serveur principal de LLVM AOT (--llvm).

Désactivez la prise en charge Bitcode ou activer la LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx : Messages d’erreur de génération de code

### <a name="mt40xx-main"></a>MT40xx : Principal

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001 : Le modèle principal ne peut pas être étendu à `*`.

Une erreur s’est produite lors de la génération main.m. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002 : Impossible de compiler le code généré pour les méthodes P/Invoke. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com

Impossible de compiler le code généré pour les méthodes P/Invoke. Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt41xx-registrar"></a>MT41xx : Inscription

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101 : Le bureau d’enregistrement ne peut pas générer une signature pour le type `*`.

Un type a été trouvé dans l’API exportée que le runtime ne sait pas comment marshaler vers/à partir d’Objective-C.

Si vous pensez Xamarin.iOS doit prendre en charge le type en question, veuillez soumettre une demande d’amélioration à [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102 : Le bureau d’enregistrement trouvé un type non valide `*` dans la signature de méthode `*`. Utilisez plutôt `*`.

Cela actuellement se produit uniquement avec un type : System.DateTime. Utilisez plutôt l’équivalent Objective-C (NSDate).

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103 : Le bureau d’enregistrement trouvé un type non valide `*` dans la signature de méthode `*`: Le type implémente INativeObject, mais n’a pas de constructeur qui prend deux (IntPtr, bool) arguments

Cela se produit lorsque le bureau d’enregistrement rencontrer un type dans une signature avec les caractéristiques mentionnées. Le bureau d’enregistrement devrez peut-être créer de nouvelles instances du type, et dans ce cas, il nécessite un constructeur avec (IntPtr, bool) signature - le premier argument (IntPtr) spécifie le handle managé, tandis que la deuxième si l’appelant transmet la propriété de natif gérer (si cette valeur est false 'conserver' sera appelée sur l’objet).

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104 : Le bureau d’enregistrement ne peut pas marshaler la valeur de retour de type `*` dans la signature de méthode `*`.

Un type a été trouvé dans l’API exportée que le runtime ne sait pas comment marshaler vers/à partir d’Objective-C.

Si vous pensez Xamarin.iOS doit prendre en charge le type en question, veuillez soumettre une demande d’amélioration à [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105 : Le bureau d’enregistrement ne peut pas marshaler le paramètre de type `*` dans la signature de méthode `*`.

Si vous pensez Xamarin.iOS doit prendre en charge le type en question, veuillez soumettre une demande d’amélioration à [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106 : Le bureau d’enregistrement ne peut pas marshaler la valeur de retour pour la structure `*` dans la signature de méthode `*`.

Un type a été trouvé dans l’API exportée que le runtime ne sait pas comment marshaler vers/à partir d’Objective-C.

Si vous pensez Xamarin.iOS doit prendre en charge le type en question, veuillez soumettre une demande d’amélioration à [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107 : Le bureau d’enregistrement ne peut pas marshaler le paramètre de type `*` dans la signature de méthode `+`.

Un type a été trouvé dans l’API exportée que le runtime ne sait pas comment marshaler vers/à partir d’Objective-C.

Si vous pensez Xamarin.iOS doit prendre en charge le type en question, veuillez soumettre une demande d’amélioration à [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108 : Le bureau d’enregistrement ne peut pas obtenir le type de ObjectiveC pour le type managé `*`.

Un type a été trouvé dans l’API exportée que le runtime ne sait pas comment marshaler vers/à partir d’Objective-C.

Si vous pensez Xamarin.iOS doit prendre en charge le type en question, veuillez soumettre une demande d’amélioration à [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109 : Impossible de compiler le code généré de bureau d’enregistrement. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com

Impossible de compiler le code généré pour le bureau d’enregistrement. Le journal de génération contient la sortie de la compilation native, expliquant pourquoi n’est pas compiler le code.

Il s’agit toujours d’un bogue dans Xamarin.iOS ; Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com) avec votre projet ou un cas de test.

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: Le bureau d’enregistrement ne peut pas marshaler le paramètre out de type `*` dans la signature de méthode `*`.

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111 : Le bureau d’enregistrement ne peut pas générer une signature pour le type `*` dans la méthode `*`.

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvancedtopicsregistrarhttpsdeveloperxamarincomguidesiosadvancedtopicsregistrar-for-more-information"></a>MT4112 : Le bureau d’enregistrement trouvé un type non valide `*`. Inscrire des types génériques avec Objective-C n’est pas pris en charge et peut entraîner un comportement aléatoire et/ou des blocages (pour descendante compatibilité avec les versions antérieures de Xamarin.iOS il est possible d’ignorer cette erreur en passant `--unsupported--enable-generics-in-registrar` comme un mtouch supplémentaires argument dans la page d’options Build du projet iOS. Consultez [developer.xamarin.com/guides/ios/advanced_topics/registrar](https://developer.xamarin.com/guides/ios/advanced_topics/registrar) pour plus d’informations).

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113 : Le bureau d’enregistrement trouvé une méthode générique : '\*.\*'. Exportation des méthodes génériques n’est pas pris en charge et entraîne un comportement aléatoire et/ou des blocages.

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114 : Erreur inattendue dans le bureau d’enregistrement pour la méthode '\*.\*'-Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116 : Ne peut pas inscrire l’assembly ' *': *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117 : Le bureau d’enregistrement trouvé une non-correspondance de signature dans la méthode ' *.* »-le sélecteur indique la méthode accepte * paramètres, alors que la méthode managée a * paramètres.

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118 : Impossible d’inscrire deux types managés («\*'et'\*') portant le même nom natif ('* ').

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119 : Le sélecteur n’a pas pu être inscrire '\*'du membre'\*. *', car le sélecteur est déjà enregistré sur un autre membre.

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120 : Le bureau d’enregistrement trouvé un type de champ inconnu '\*« dans le champ »\*. * ». Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com

Cette erreur indique un bogue dans Xamarin.iOS. Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121 : Impossible d’utiliser GCC / G ++ pour compiler le code généré à partir du bureau d’enregistrement statique lors de l’utilisation de l’infrastructure de comptes (les fichiers d’en-tête fournies par Apple utilisé lors de la compilation nécessitent Clang). Utilisez Clang (--du compilateur : clang) ou le bureau d’enregistrement dynamique (--registrar : dynamiques).

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122 : Impossible d’utiliser le compilateur Clang fourni dans le *.* Kit de développement logiciel pour compiler le code généré à partir du bureau d’enregistrement statique lors de la non-ASCII noms de types ('* ') sont présents dans l’application. Utilisez GCC / G ++ (--du compilateur : gcc | g ++), le bureau d’enregistrement dynamique (--registrar : dynamiques) ou un kit de développement logiciel plus récent.

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123 : Le type du paramètre variadique dans la fonction variadique ' *' doit être System.IntPtr.

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124 : Non valide * trouvé sur ' *'. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com

Cette erreur indique un bogue dans Xamarin.iOS. Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125 : Le bureau d’enregistrement trouvé un type non valide '\*« dans la signature de méthode'\*' : L’interface doit avoir un attribut de protocole en spécifiant son type de wrapper.

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126 : Impossible d’inscrire deux protocoles managés ('\*'et'\*') portant le même nom natif ('* ').

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127 : Impossible d’inscrire plusieurs méthodes d’interface pour la méthode '\*» (qui implémente '\*').

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128 : Le bureau d’enregistrement trouvé un type de paramètre générique non valide '\*« dans la méthode'\*'. Le paramètre générique doit avoir une contrainte « NSObject ».

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129 : Le bureau d’enregistrement trouvé un type de retour générique non valide '\*« dans la méthode'\*'. Le type de retour générique doit avoir une contrainte « NSObject ».

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: Le bureau d’enregistrement ne peut pas exporter les méthodes statiques dans les classes génériques (« * »).

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: Le bureau d’enregistrement ne peut pas exporter les propriétés statiques de classes génériques («\*.\*').

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132 : Le bureau d’enregistrement trouvé un type de retour générique non valide '\*« dans la propriété'\*'. Le type de retour doit avoir une contrainte de « NSObject ».

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133 : Impossible de construire une instance du type « * » à partir d’Objective-C, car le type est générique. [Exception Runtime]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134 : À l’aide de votre application est la ' *' framework, qui n’est pas inclus dans le Kit de développement logiciel que vous utilisez pour générer votre application iOS (cette infrastructure a été introduite dans iOS *, tandis que vous créez avec iOS * SDK.) Veuillez sélectionner un kit de développement logiciel plus récent dans des options Build iOS de votre application.

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135 : Le membre '\*.\*' possède un attribut d’exportation qui ne spécifie pas un sélecteur. Un sélecteur est requis.

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136 : Le bureau d’enregistrement ne peut pas marshaler le type de paramètre '\*'du paramètre'\*« dans la méthode'\*. * »

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138 : Le bureau d’enregistrement ne peut pas marshaler le type de propriété '\*« de la propriété'\*. * ».

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139 : Le bureau d’enregistrement ne peut pas marshaler le type de propriété '\*« de la propriété'\*. * ». Propriétés avec l’attribut [connexion] doivent avoir un type de propriété NSObject (ou une sous-classe NSObject).

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140 : Le bureau d’enregistrement trouvé une non-correspondance de signature dans la méthode ' *.* »-le sélecteur indique la méthode variadiques prend * paramètres, alors que la méthode managée a * paramètres.

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141 : Impossible d’inscrire le sélecteur '\*« sur le membre'\*', car Xamarin.iOS inscrit implicitement ce sélecteur.

Cela se produit lorsque le sous-classement d’un type de framework et essayez d’implémenter un « conserver', 'version' ou 'dealloc' méthode :

```csharp
class MyNSObject : NSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

Il est toutefois possible de substituer ces méthodes si la classe n’est pas la première sous-classe de l’infrastructure de type :

```csharp
class MyNSObject : NSObject
{
}

class MyCustomNSObject : MyNSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

Dans ce cas remplacent Xamarin.iOS `retain`, `release` et `dealloc` sur la `MyNSObject` (classe), et il n’existe aucun conflit.

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142 : Impossible d’inscrire le type ' *'.

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143 : La classe ObjectiveC ' *' ne peut pas être inscrit, il ne semble pas dériver à partir de n’importe quelle classe ObjectiveC connu (y compris le NSObject).

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144 : Impossible d’inscrire la méthode ' *', car il n’a pas un trampoline associé. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com.

Cela indique un bogue dans Xamarin.iOS. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145 : Enum non valide ' *': les enums avec l’attribut [natif] doit avoir un type enum sous-jacent de 'longs' ou 'ulong'.

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146 : Le paramètre de nom de l’attribut de bureau d’enregistrement sur la classe\*' ('\*») contient un caractère non valide : '\*» (\*).

Le nom d’une classe Objectice-C ne peut pas contenir d’espace blanc, ce qui signifie que le `Register` attribut sur la classe managée correspondante ne peut pas avoir un `Name` paramètre ne peut pas contenir d’espaces blancs soit.

Vérifiez que le `Register` attribut sur la classe managée mentionnée dans le message d’erreur ne contient pas de tout espace blanc.

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147 : A détecté un protocole héritant du protocole JSExport lors de l’utilisation du bureau d’enregistrement dynamique. Il n’est pas possible d’exporter des protocoles à JavaScriptCore dynamiquement ; Vous devez utiliser le bureau d’enregistrement statique (ajouter '--bureau d’enregistrement : static aux arguments mtouch supplémentaires dans des options Build iOS du projet pour sélectionner le bureau d’enregistrement statique).

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148 : Le bureau d’enregistrement trouvé un protocole générique : ' *'. Exportation des protocoles génériques n’est pas prise en charge.

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149 : Impossible d’inscrire la méthode '\*.\*', car le type du premier paramètre («\*') ne correspond pas au type de catégorie ('\*').

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150 : Impossible d’inscrire le type '\*', car la propriété Type ('\*') dans sa catégorie attribut n’hérite pas NSObject.

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151 : Impossible d’inscrire le type ' *', car la propriété de Type dans son attribut de catégorie n’est pas définie.

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152 : Impossible d’inscrire le type ' *' en tant que catégorie, car il implémente INativeObject ou une sous-classe NSObject.

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153 : Impossible d’inscrire le type '\*' en tant que catégorie, car elle est générique.

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154 : Impossible d’inscrire la méthode '\*' en tant que catégorie méthode, car elle est générique.

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155 : Impossible d’inscrire la méthode '\*« avec le sélecteur'\*' comme une méthode de catégorie sur ' *', car le Objective-C possède déjà une implémentation pour ce sélecteur.

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156 : Impossible d’inscrire deux catégories («\*'et'\*') portant le même nom natif ('* ').

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157 : Impossible d’inscrire la méthode de la catégorie «\*', car au moins un paramètre est requis (et son type doit correspondre au type de catégorie '\*')

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158 : Impossible d’inscrire le constructeur * dans la catégorie *, car les constructeurs dans les catégories ne sont pas pris en charge.

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159 : Impossible d’inscrire la méthode ' *' en tant que catégorie méthode, car les méthodes de la catégorie doivent être statiques.

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160: Caractère non valide '\*» (\*) trouvé dans le sélecteur '\*'for'\*'.

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: Le bureau d’enregistrement trouvé une structure non pris en charge «\*» : Tous les champs dans une structure doivent également être des structures (champ «\*« avec le type'{2}' n’est pas une structure).

Le bureau d’enregistrement trouvé une structure avec des champs non pris en charge.

Tous les champs dans une structure qui est exposé à Objective-C doivent également être des structures (pas de classes).

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162 : Le type '\*» (utilisé en tant que * {2}) n’est pas disponible dans ** (elle a été introduite dans * *)\* générez avec une version plus récente * Kit de développement logiciel (généralement effectuée à l’aide de la version la plus récente de Xcode.

Le bureau d’enregistrement trouvé un type qui n’est pas inclus dans le kit SDK actuel.

Mettez à niveau Xcode.

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163 : Erreur interne dans le bureau d’enregistrement (*). Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com

Cette erreur indique un bogue dans Xamarin.iOS. Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164 : Impossible d’exporter la propriété '\*', car son sélecteur '\*' est un mot clé Objective-C. Utilisez un autre nom.

Le sélecteur pour la propriété en question n’est pas un identificateur valid de Objective-C.

Utilisez un identificateur Objective-C valide en tant que les sélecteurs.

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165 : Le bureau d’enregistrement n’a pas pu trouver le type 'System.Void' dans un des assemblys référencés.

Cette erreur probablement indique un bogue dans Xamarin.iOS. Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166 : Impossible d’inscrire la méthode '\*', car la signature contient un type (\*) qui n’est pas un type référence.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167 : Impossible d’inscrire la méthode '\*', car la signature contient un type générique (\*) avec un type d’argument générique qui n’est pas une sous-classe NSObject (*).

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managedname-because-its-objective-c-name-exportedname-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168 : Impossible d’inscrire le type ' {gérés\_nom}', car son nom Objective-C ' {exporté\_nom}' est un mot clé Objective-C. Utilisez un autre nom.

Le nom de Objective-C pour le type en question n’est pas un identificateur valide de Objective-C.

Utilisez un identificateur valide de Objective-C.

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169 : Impossible de générer un wrapper de P/Invoke pour {méthode} : {message}

Échec de Xamarin.iOS générer une fonction wrapper de P/Invoke pour indiquée.
Vérifiez le message d’erreur signalées pour la cause sous-jacente.

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170 : Le bureau d’enregistrement ne peut pas convertir à partir de '{type}' managé '{type natif}' pour la valeur de retour dans la méthode {méthode}.

Consultez la description de l’erreur <a href="#MT4172">MT4172</a>.

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171 : L’attribut BindAs sur le membre {membre} n’est pas valide : le type de BindAs {type} est différent du type de propriété {type}.

Vérifiez que le type dans l’attribut BindAs correspond au type du membre, qu'il est attaché.

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172 : Le bureau d’enregistrement ne peut pas convertir à partir de '{type natif}' '{type}' managé pour le paramètre '{nom du paramètre}' dans la méthode {méthode}.

Le bureau d’enregistrement ne prend pas en charge la conversion entre les types mentionnés.

Il s’agit un bogue dans Xamarin.iOS si l’API en question est fourni par Xamarin.iOS ; Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ][1].

Si vous rencontrez ce problème lors du développement d’un projet de liaison pour une bibliothèque native, nous sommes ouverts à l’ajout de prise en charge de nouvelles combinaisons de types. Si c’est le cas, envoyez une demande d’amélioration ([http://bugzilla.xamarin.com][2]) avec un test de cas et nous allons évaluez-le.

[1]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS
[2]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS&component=General&bug_severity=enhancement

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx : Messages d’erreur GCC et chaîne d’outils

### <a name="mt51xx-compilation"></a>MT51xx : Compilation

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101 : Manquant ' *' compilateur. Installez Xcode composant « Outils de ligne de commande »

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102 : Échec pour assembler le fichier ' *'. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103 : Impossible de compiler le fichier ' *'. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104 : Ni n’a pas trouvé le «\*'ni le'\*' compilateur. Installez Xcode composant « Outils de ligne de commande »

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106 : Impossible de compiler l’ou les fichiers ' *'. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt52xx-linking"></a>MT52xx : Liaison

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201: Échec de la liaison natif. Passez en revue le journal de génération et les indicateurs d’utilisateur fournis à gcc : *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202 : Échec de la liaison natif. Passez en revue le journal de génération.

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203 : Natif liaison Avertissement : *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209 : Natif de liaison d’erreur : *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210: Natif liaison ayant échoué, undefined symbole : *. Vérifiez que toutes les infrastructures nécessaires ont été référencés et bibliothèques natives sont correctement liées.

Cela se produit lorsque l’éditeur de liens natif ne peut pas rechercher un symbole qui est référencé quelque part. Il existe plusieurs raisons, que cela peut se produire :

* Une liaison de tiers requiert une infrastructure, mais la liaison ne spécifie pas cela dans son `[LinkWith]` attribut. Solutions :
  - Si vous êtes l’auteur de la liaison de tiers, ou avez accès à sa source, modifier la liaison `[LinkWith]` attribut pour inclure l’infrastructure dont il a besoin :

            [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]

  - Si vous ne pouvez pas modifier la liaison de tiers, vous pouvez lier manuellement avec l’infrastructure requise en passant `-gcc_flags '-framework SystemFramework'` à `mtouch` (cela en modifiant les arguments mtouch supplémentaires dans la page d’options Build du projet iOS. N’oubliez pas que cette opération doit être effectuée pour chaque configuration de projet).
* Dans certains cas une liaison managée est composée de plusieurs bibliothèques natives, et tout doit être inclus dans les liaisons. Il est possible d’avoir plus d’une bibliothèque native dans chaque projet de liaison, par conséquent, la solution est d’ajouter simplement les bibliothèques natives requises pour le projet de liaison.</li>
* Une liaison managée fait référence à des symboles natives qui n’existent pas dans la bibliothèque native.
    Cela se produit généralement lorsqu’une liaison a existé pendant un certain temps, et le code natif a été modifié pendant cette période afin qu’une classe native particulière a été supprimée ou renommée, tandis que la liaison n’a pas été mis à jour.
* Un P/Invoke fait référence à un symbole natif qui n’existe pas. En commençant par la version 7.4 de Xamarin.iOS une <a href="#MT5214">MT5214</a> erreur sera signalée pour ce cas (pour plus d’informations, consultez MT5214).
* Une liaison de tiers / bibliothèque a été créée à l’aide de C++, mais la liaison ne spécifie pas cela dans son `[LinkWith]` attribut. Cela est généralement assez facile de reconnaître, étant donné que les symboles ont sont tronqués symboles C++ (un exemple courant est `__ZNKSt9exception4whatEv`).
  - Si vous êtes l’auteur de la liaison de tiers, ou avez accès à sa source, modifier la liaison `[LinkWith]` attribut à définir le `IsCxx` indicateur :

            [LinkWith ("mylib.a", IsCxx = true)]

  - Si vous ne pouvez pas modifier la liaison de tiers ou si vous créez un lien manuellement avec une bibliothèque tierce, vous pouvez définir l’indicateur équivalent en passant <code>-cxx</code> à mtouch (cela en modifiant les arguments mtouch supplémentaires dans la page d’options Build du projet iOS . N’oubliez pas que cette opération doit être effectuée pour chaque configuration de projet).

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211: Natif liaison ayant échoué, undefined classe Objective-C : \*. Le symbole «\*» est introuvable dans les bibliothèques ou des infrastructures liés à votre application.

Cela se produit lorsque l’éditeur de liens natif ne peut pas trouver une classe Objective-C qui est référencée quelque part. Il existe plusieurs raisons, cela peut se produire : identique à celle de [MT5210](#MT5210) et, en outre :

* Une liaison de tiers lié un protocole Objective-C, mais ne pas annoter avec le <code>[Protocol]</code> attribut dans sa définition d’api. Solutions :
  - Ajouter le manquant `[Protocol]` attribut :

              [BaseType (typeof (NSObject))]
              [Protocol] // Add this
              public interface MyProtocol
              {
              }

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212: Natif liaison ayant échoué, dupliquer le symbole : *.

Cela se produit lorsque l’éditeur de liens natif rencontre des symboles en double entre toutes les bibliothèques natives. Ce message d’erreur Il peut y avoir un ou plusieurs [MT5213](#MT5213) erreurs avec l’emplacement pour chaque occurrence du symbole. Raisons possibles de cette erreur :

* La même bibliothèque native est incluse deux fois.
* Deux bibliothèques natives distinctes se produisent pour définir les mêmes symboles.
* Une bibliothèque native n’est pas correctement construite et contient le même symbole plusieurs fois.
  Vous pouvez le vérifier à l’aide de l’ensemble de commandes à partir d’un terminal suivant (remplacez i386 par x86_64/armv7/armv7s/arm64 en fonction de l’architecture que vous créez pour) :

        # Native libraries are usually fat libraries, containing binary code for
        # several architectures in the same file. First we extract the binary
        # code for the architecture we're interested in.
        lipo libNative.a -thin i386 -output libNative.i386.a

        # Now query the native library for the duplicated symbol.
        nm libNative.i386.a | fgrep 'SYMBOL'

        # You can also list the object files inside the native library.
        # In most cases this will reveal duplicated object files.
        ar -t libNative.i386.a

  Il existe quelques méthodes possibles pour résoudre ce problème :

  - Demande que le fournisseur de la bibliothèque native résoudre le problème et de fournit la version mise à jour.
  - Résoudre vous-même en supprimant les fichiers d’objet supplémentaire (fonctionne uniquement si le problème est en fait des fichiers objets en double)

            # Find out if the library is a fat library, and which
            # architectures it contains.
            lipo -info libNative.a

            # Extract each architecture (i386/x86_64/armv7/armv7s/arm64) to a separate file
            lipo libNative.a -thin ARCH -output libNative.ARCH.a

            # Extract the object files for the offending architecture
            # This will remove the duplicates by overwriting them
            # (since they have the same filename)
            mkdir -p ARCH
            cd ARCH
            ar -x ../libNative.ARCH.a

            # Reassemble the object files in an .a
            ar -r ../libNative.ARCH.a *.o
            cd ..

            # Reassemble the fat library
            lipo *.a -create -output libNative.a

  - Demandez à l’éditeur de liens à supprimer du code non utilisé. Xamarin.iOS fait cela automatiquement si toutes les conditions suivantes sont remplies :
    - Toutes les liaisons tiers `[LinkWith]` attributs ont activé SmartLink :

            [assembly: LinkWith ("libNative.a", SmartLink = true)]

    - Ne `-gcc_flags` est passé à mtouch (dans le champ arguments mtouch supplémentaires, d’e/s des options de génération du projet).
    - Il est également possible de demander de l’éditeur de liens directement supprimer le code inutilisé en ajoutant `-gcc_flags -dead_strip` aux arguments mtouch supplémentaires dans les e/s du projet des options de génération.

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213 : Symbole dupliqué dans : * (emplacement associé à l’erreur précédente)

Cette erreur est signalée uniquement avec [MT5212](#MT5212). Consultez [MT5212](#MT5212) pour plus d’informations.

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214 : Natif liaison ayant échoué, undefined symbole : *. Ce symbole a été référencé le membre géré *. Vérifiez que toutes les infrastructures nécessaires ont été les bibliothèques natives et référencés liés.

Cette erreur est signalée lorsque le code managé contient un P/Invoke vers une méthode native qui n’existe pas. Par exemple :

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Il existe quelques solutions possibles :

- Supprimer le P/Invoke en question à partir du code source.
- Activer l’éditeur de liens managé pour tous les assemblys (cela en des options Build iOS du projet, en définissant « Comportement de l’éditeur de liens » à « Tous les assemblys »). Cela supprimera efficacement tous le P/Invoke vous n’utilisez pas de l’application (automatiquement, au lieu de manuellement comme le point précédent). L’inconvénient est que cela rendra vos builds du simulateur un peu plus lent, et elle peut endommager votre application si elle utilise la réflexion, vous pouvez trouver plus d’informations sur l’éditeur de liens [ici](~/ios/deploy-test/linker.md) )
- Créer une deuxième bibliothèque native qui contient les symboles natifs manquants. Notez qu’il s’agit simplement d’une solution de contournement (s’il vous arrive d’essaie d’appeler ces fonctions, votre application se bloque).

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215 : Fait référence à « * » peut nécessiter supplémentaires - framework = XXX ou lXXX - instructions pour l’éditeur de liens natif

Il s’agit d’un avertissement indiquant qu’un P/Invoke a été détecté pour référencer la bibliothèque en question, mais l’application n’est pas liaison avec elle.

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216 : Échec de la liaison natif pour *. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com

Cette erreur est signalée lors de la liaison de la sortie de la compilation AOT.

Cette erreur probablement indique un bogue dans Xamarin.iOS. Veuillez soumettre un rapport de bogue à [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217 : Liaison native si elle a échoué, car la ligne de commande de l’éditeur de liens a été trop longue (* caractères).

Échec de la liaison natif, et il est possible du fait que la commande de l’éditeur de liens a été trop longue.

Projets Xamarin.iOS fait souvent référence symboles natifs dynamiquement, ce qui signifie que l’éditeur de liens natif peut supprimer des symboles natifs pendant le processus de liaison natif, étant donné que l’éditeur de liens natif ne voit pas que ces symboles sont utilisés.

Généralement Xamarin.iOS demandera à l’éditeur de liens natif pour conserver des symboles à l’aide de la `-u symbol` indicateur de l’éditeur de liens, mais s’il existe de nombreuses des symboles, l’ensemble de ligne de commande peut dépasser la longueur maximale de ligne de commande tel que spécifié par le système d’exploitation.

Il existe quelques sources possibles pour ces symboles dynamiques :

* P/Invoke aux méthodes dans les bibliothèques liées statiquement (où le nom de la dll est `__Internal` dans l’attribut DllImport `[DllImport ("__Internal")]`).
* Champ de références à des emplacements de mémoire dans les bibliothèques liées statiquement à partir de projets de liaison (`[Field]` attributs).
* Classes objective-C référencé dans les bibliothèques liées statiquement à partir de projets de liaison (lors des builds incrémentielles ou ne pas à l’aide du bureau d’enregistrement statique).

Solutions possibles :

* Activer l’éditeur de liens géré (si possible pour tous les assemblys au lieu des assemblys SDK uniquement). Cela peut supprimer suffisante des sources pour les symboles dynamiques afin que l’éditeur de liens 's de ligne de commande n’a dépassé la valeur maximale.
* Réduisez le nombre de P/Invoke, des références de champ et/ou des classes d’Objective-C.
* Réécrivez les symboles dynamiques pour un nom plus court.
* Transmettez `-dlsym:false` comme argument mtouch supplémentaires dans iOS du projet des options de génération. Avec cette option, Xamarin.iOS génère une référence native dans le code compilé en AOT et n’avez pas besoin de demander de l’éditeur de liens pour conserver ce symbole. Toutefois, ne fonctionne que pour appareil génère, et elle entraîne des erreurs de l’éditeur de liens absence de P/Invoke vers des fonctions qui n’existent pas dans la bibliothèque statique.
* Passer `--dynamic-symbol-mode=code` en tant qu’arguments mtouch supplémentaires dans iOS du projet des options de génération. Avec cette option, Xamarin.iOS génère le code natif supplémentaire qui fait référence à ces symboles au lieu de demander de l’éditeur de liens natif pour conserver ces symboles à l’aide des arguments de ligne de commande. L’inconvénient de cette approche est qu’il augmente quelque peu la taille de l’exécutable.
* Activer le bureau d’enregistrement statique en passant `--registrar:static` comme argument mtouch supplémentaires dans iOS du projet Build options (pour les builds de simulateur, étant donné que le bureau d’enregistrement statique est déjà la valeur par défaut pour les builds d’appareil). Le bureau d’enregistrement statique générera le code qui référence les classes Objective-C de manière statique, il est donc pas nécessaire pour demander de l’éditeur de liens natif pour conserver ces classes.
* Désactiver les builds incrémentielles (pour les builds d’appareil). Lorsque les builds incrémentielles sont activées, le code généré par le bureau d’enregistrement statique ne sont pas considéré par l’éditeur de liens natif, ce qui signifie que Xamarin.iOS doit demander toujours conserver l’éditeur de liens référencé les classes Objective-C. Par conséquent, la désactivation des builds incrémentielles empêchera ce besoin.

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218 : Impossible d’ignorer le symbole dynamique {symbole} (--symbole dynamique ignorer = {symbol}), car il n’a pas été détecté comme un symbole dynamique.

L’argument de ligne de commande `--ignore-dynamic-symbol=symbol` a été passé, mais ce symbole n’est pas un symbole qui a été reconnu comme un symbole dynamique qui doit être conservé manuellement.

Il existe deux raisons à cela :

* Le nom du symbole est incorrect.
    * Ne faites pas précéder un trait de soulignement pour le nom du symbole.
    * Le symbole pour les classes d’Objective-C est `OBJC_CLASS_$_<classname>`.
* Le symbole est correct, mais il est un symbole qui est déjà conservé par des moyens normaux (certains génération causes d’options la liste exacte des symboles dynamiques pour faire varier).

### <a name="mt53xx-other-tools"></a>MT53xx : Autres outils

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301 : Outil de « supprimer » manquant. Installez Xcode composant « Outils de ligne de commande »

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302 : Outil de 'dsymutil' manquant. Installez Xcode composant « Outils de ligne de commande »

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303 : Impossible de générer les symboles de débogage (répertoire dSYM). Passez en revue le journal de génération.

Une erreur s’est produite lors de l’exécution de dsymutil sur le répertoire .app finale pour créer les symboles de débogage. Veuillez consulter le journal de génération pour voir la sortie de dsymutil et voir comment elle peut être résolue.

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304 : Impossible de supprimer le fichier binaire final. Passez en revue le journal de génération.

Une erreur s’est produite lors de l’exécution de l’outil « bande » pour supprimer les informations de débogage de l’application.

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305 : Outil de 'lipo' manquant. Installez Xcode composant « Outils de ligne de commande »

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306 : Impossible de créer l’une bibliothèque fat. Passez en revue le journal de génération.

Une erreur s’est produite lors de l’exécution de l’outil « lipo ». Veuillez consulter le journal de génération pour voir l’erreur signalée par « lipo ».

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307 : Impossible de signer l’exécutable. Passez en revue le journal de génération.

Une erreur s’est produite lors de l’inscription de l’application. Veuillez consulter le journal de génération pour voir l’erreur signalée par « codesign ».

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx : messages d’erreur des outils mtouch interne

### <a name="mt600x-stripper"></a>MT600x : Reformatage

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001 : Version en cours d’exécution de Cecil ne prend pas en charge la suppression d’assembly

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002 : Ne peut pas supprimer assembly `*`.

Une erreur s’est produite lors de la suppression du code managé (en supprimant le code de langage intermédiaire) à partir des assemblys dans l’application.

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003 : [Message UnauthorizedAccessException]

Une erreur de sécurité s’est produite lors de la suppression des symboles à partir de l’application de débogage.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx : Messages d’erreur MSBuild

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001 : N’a pas pu résoudre l’hôte des adresses IP pour les paramètres du débogueur Wi-Fi.

*Tâche MSBuild : DetectDebugNetworkConfigurationTaskBase*

Étapes de dépannage :

- Essayez d’exécuter `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (qui devrait vous donner une adresse IP et non une erreur évidemment).
- Essayez d’exécuter « ping \`nom d’hôte\`» qui peut vous donner plus d’informations, par exemple : `cannot resolve MyHost.local: Unknown host`

Dans certains cas, il s’agit d’un problème de « réseau local » et il peut être résolu en ajoutant l’hôte inconnu `127.0.0.1   MyHost.local` dans `/etc/hosts`.

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002 : Cet ordinateur n’a pas de cartes réseau. Cela est nécessaire lors du débogage ou de profilage sur l’appareil en Wi-Fi.

*Tâche MSBuild : DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003 : L’Extension d’application ' *' ne contient pas d’un fichier Info.plist.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004 : L’Extension d’application « * » ne spécifie pas un CFBundleIdentifier.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005 : L’Extension d’application « * » ne spécifie pas un CFBundleExecutable.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006 : L’Extension d’application «\*' a un CFBundleIdentifier non valide (\*), il ne commence pas par CFBundleIdentifier du faisceau de l’application principale (*).

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007 : L’Extension d’application «\*' a un CFBundleIdentifier (\*) qui se termine par le suffixe non conforme « .key ».

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008 : L’Extension d’application « * » ne spécifie pas un CFBundleShortVersionString.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009 : L’Extension d’application ' *' a un Info.plist non valide : il ne contient pas un dictionnaire NSExtension.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010 : L’Extension d’application ' *' a un Info.plist non valide : le dictionnaire NSExtension ne contienne pas une valeur NSExtensionPointIdentifier.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011 : Le WatchKit Extension ' *' a un Info.plist non valide : le dictionnaire NSExtension ne contient-elle pas un dictionnaire NSExtensionAttributes.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012 : Le WatchKit Extension ' *' n’a pas exactement une application watch.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013 : Le WatchKit Extension ' *' a un Info.plist non valide : UIRequiredDeviceCapabilities doit contenir la fonctionnalité « watch-Compagnon ».

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014 : L’application Watch ' *' ne contient pas d’un fichier Info.plist.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015 : L’application Watch ' *' ne spécifie pas un CFBundleIdentifier.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016 : L’application Watch '\*' a un CFBundleIdentifier non valide (\*), il ne commence pas par CFBundleIdentifier du faisceau de l’application principale (*).

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017 : L’application Watch '\*' n’a pas de valeur UIDeviceFamily valide. Attendu 'Watch (4)', mais trouvé '\* (*) ».

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018 : L’application Watch ' *' ne spécifie pas un CFBundleExecutable

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019 : L’application Watch '\*' a une valeur WKCompanionAppBundleIdentifier non valide ('\*'), CFBundleIdentifier du regroupement de l’application principale ne correspond pas ('* ').

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020 : L’application Watch ' *' a un Info.plist non valide : la clé WKWatchKitApp doit être présent et a la valeur 'true'.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021 : L’application Watch ' *' a un Info.plist non valide : la clé LSRequiresIPhoneOS ne doit pas être présente.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022 : L’application Watch ' *' ne contient pas une Extension Watch.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023 : L’Extension Watch ' *' ne contient pas d’un fichier Info.plist.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024 : L’Extension Watch ' *' ne spécifie pas un CFBundleIdentifier.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025 : L’Extension Watch ' *' ne spécifie pas un CFBundleExecutable.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026 : L’Extension Watch '\*' a un CFBundleIdentifier non valide (\*), il ne commence pas par CFBundleIdentifier du faisceau de l’application principale (*).

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027 : L’Extension Watch '\*' a un CFBundleIdentifier (\*) qui se termine par le suffixe non conforme « .key ».

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028 : L’Extension Watch ' *' a un Info.plist non valide : il ne contient pas un dictionnaire NSExtension.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029 : L’Extension Watch ' *' a un Info.plist non valide : le NSExtensionPointIdentifier doit être « com.apple.watchkit ».

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030 : L’Extension Watch ' *' a un Info.plist non valide : le dictionnaire NSExtension doit contenir NSExtensionAttributes.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031 : L’Extension Watch ' *' a un Info.plist non valide : le dictionnaire NSExtensionAttributes doit contenir un WKAppBundleIdentifier.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032 : Le WatchKit Extension ' *' a un Info.plist non valide : UIRequiredDeviceCapabilities ne doit pas contenir de la fonctionnalité « watch-Compagnon ».

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033 : L’application Watch ' *' ne contient pas d’un fichier Info.plist.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034 : L’application Watch ' *' ne spécifie pas un CFBundleIdentifier.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035 : L’application Watch '\*' n’a pas de valeur UIDeviceFamily valide. Attendu '\*«, mais détection »\* (\*) ».

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036 : L’application Watch ' *' ne spécifie pas un CFBundleExecutable.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037 : Le WatchKit Extension '{extensionName}' a une valeur WKAppBundleIdentifier non valide ('\*»), il ne correspond pas à CFBundleIdentifier l’application Watch («\*»).

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038 : L’application Watch ' *' a un Info.plist non valide : le WKCompanionAppBundleIdentifier doit exister et doit correspondre à CFBundleIdentifier du faisceau de l’application principale.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039 : L’application Watch ' *' a un Info.plist non valide : la clé LSRequiresIPhoneOS ne doit pas être présente.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040 : Le bundle d’applications {AppBundlePath} ne contient-elle pas d’un fichier Info.plist.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041 : Chemin d’accès du fichier Info.plist principal ne spécifie pas un CFBundleIdentifier.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042 : Chemin d’accès du fichier Info.plist principal ne spécifie pas un CFBundleExecutable.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043 : Chemin d’accès du fichier Info.plist principal ne spécifie pas un CFBundleSupportedPlatforms.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044 : Chemin d’accès du fichier Info.plist principal ne spécifie pas un UIDeviceFamily.

*Tâche MSBuild : ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045 : Non reconnu de Format : *.

*Tâche MSBuild : PropertyListEditorTaskBase*

Où * peut être :

- string
- array
- dict
- bool
- real
- integer
- date
- data

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046 : Ajouter Entrée, *, incorrectement spécifié.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047 : Ajouter Entrée, *, contient l’Index de tableau non valide.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048 : Ajouter : * entrée existe déjà.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049 : Ajouter Impossible d’ajouter l’entrée, *, vers le Parent.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050 : Supprimer : Impossible de supprimer l’entrée, *, à partir du Parent.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051 : Supprimer : Entrée, *, contient l’Index de tableau non valide.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052 : Supprimer : Entrée, *, n’existe pas.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053 : Importation : Entrée, *, incorrectement spécifié.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054 : Importation : Entrée, *, contient l’Index de tableau non valide.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055 : Importation : Erreur de lecture du fichier : *.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056 : Importation : Impossible d’ajouter l’entrée, *, vers le Parent.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057 : Fusion : Impossible d’ajouter des entrées de tableau au dictionnaire

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058 : Fusion : Entrée spécifiée doit être un conteneur.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059 : Fusion : Entrée, *, contient l’Index de tableau non valide.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060 : Fusion : Entrée, *, n’existe pas.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061 : Fusion : Erreur de lecture du fichier : *.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062 : Définissez : Entrée, *, incorrectement spécifié.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063 : Définissez : Entrée, *, contient l’Index de tableau non valide.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064 : Définissez : Entrée, *, n’existe pas.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065 : Action de l’éditeur PropertyList inconnue : *.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066 : Erreur lors du chargement ' *': *.

*Tâche MSBuild : PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067 : Erreur durant l’enregistrement ' *': *.

*Tâche MSBuild : PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx : Messages d’erreur d’exécution

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001 : Incompatibilité de version entre le runtime Xamarin.iOS natif et monotouch.dll. Réinstallez Xamarin.iOS.

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002 : Impossible de trouver la méthode '\*« dans le type'\*'.

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003 : Impossible de trouver la méthode générique fermée '\*'sur le type'\*'.

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004 : Impossible de créer une instance de * pour l’objet natif 0 x * (de type ' *'), car une autre instance existe déjà pour cet objet natif (de type *).

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005 : Type de wrapper '\*'manque sa classe ObjectiveC natif'\*'.

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006 : Impossible de trouver le sélecteur '\*'sur le type'\*'

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007 : Impossible d’obtenir le descripteur de méthode pour le sélecteur '\*'sur le type'\*', car le sélecteur ne correspond pas à une méthode

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008 : La version chargée de Xamarin.iOS.dll a été compilée pour * bits, tandis que le processus est * bits. Veuillez soumettre un bogue sur http://bugzilla.xamarin.com.

Cela indique que quelque chose ne va pas dans le processus de génération. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009 : Impossible de localiser le bloc pour la méthode de conversion pour la méthode déléguée *.* ' s paramètre #*. Veuillez soumettre un bogue sur http://bugzilla.xamarin.com.

Cela indique une API n’a pas été correctement liée. S’il s’agit d’une API exposée par Xamarin, veuillez soumettre un bogue dans notre bugzilla ([http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)), s’il s’agit d’une liaison par des tiers, contactez le fournisseur.

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010 : Incompatibilité de taille de type natif entre Xamarin. [iOS | Mac] .dll et l’architecture en cours d’exécution. Xamarin. [iOS | Mac] .dll a été conçu pour *-bit, tandis que le processus en cours est *-bit.

Cela indique que quelque chose ne va pas dans le processus de génération. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011 : Impossible de localiser le délégué pour l’attribut block de conversion ([DelegateProxy]) pour la valeur de retour pour la méthode *.* . Veuillez soumettre un bogue sur http://bugzilla.xamarin.com.

Xamarin.iOS n’a pas pu trouver une méthode requise lors de l’exécution (convertir un délégué à un bloc).

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012 : DelegateProxyAttribute non valide pour la valeur de retour pour la méthode *.* : Type_délégué est null. Veuillez soumettre un bogue sur http://bugzilla.xamarin.com.

L’attribut DelegateProxy pour la méthode en question n’est pas valide.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013 : DelegateProxyAttribute non valide pour la valeur de retour pour la méthode *.* : Type_délégué ({2}) spécifie un type de champ 'Handler'. Veuillez soumettre un bogue sur http://bugzilla.xamarin.com.

L’attribut DelegateProxy pour la méthode en question n’est pas valide.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014 : DelegateProxyAttribute non valide pour la valeur de retour pour la méthode *.* : Le Type_délégué ({2}) le champ 'Handler' est null. Veuillez soumettre un bogue sur http://bugzilla.xamarin.com.

L’attribut DelegateProxy pour la méthode en question n’est pas valide.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015 : DelegateProxyAttribute non valide pour la valeur de retour pour la méthode *.* : Le Type_délégué ({2}) champ de 'Handler' n’est pas un délégué, il est un *. Veuillez soumettre un bogue sur http://bugzilla.xamarin.com.

L’attribut DelegateProxy pour la méthode en question n’est pas valide.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016 : Impossible de convertir le délégué pour bloquer pendant la valeur de retour pour la méthode *.* , car l’entrée n’est pas un délégué, il est un *. Veuillez soumettre un bogue sur http://bugzilla.xamarin.com.

L’attribut DelegateProxy pour la méthode en question n’est pas valide.

Cela indique généralement un bogue dans Xamarin.iOS ; Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018 : Erreur de cohérence interne. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com.

Cela indique un bogue dans Xamarin.iOS. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019 : Impossible de trouver l’assembly * dans les assemblys chargés.

Cela indique un bogue dans Xamarin.iOS. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020 : N’a pas trouvé le module avec MetadataToken * dans l’assembly *.

Cela indique un bogue dans Xamarin.iOS. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021 : Type de jeton implicit inconnu : *.

Cela indique un bogue dans Xamarin.iOS. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022 : Attendu de la référence de jeton * soit un *, mais c’est un *. Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com.

Cela indique un bogue dans Xamarin.iOS. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023 : Un objet d’instance est requis pour construire une méthode générique fermée pour la méthode générique ouverte : * (référence de jeton : *). Veuillez soumettre un rapport de bogue à http://bugzilla.xamarin.com.

Cela indique un bogue dans Xamarin.iOS. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smarttype-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024 : Un type d’extension valide pour l’énumération smart '{smart_type}' est introuvable. Veuillez soumettre un bogue sur https://bugzilla.xamarin.com.

Cela indique un bogue dans Xamarin.iOS. Veuillez soumettre un bogue sur [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).
