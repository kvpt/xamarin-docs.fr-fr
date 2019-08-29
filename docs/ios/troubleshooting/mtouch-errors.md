---
title: Erreurs Xamarin. iOS
description: Ce document décrit les différentes erreurs générées par mTouch, l’outil utilisé pour regrouper les applications Xamarin. iOS. Les erreurs sont répertoriées par code et sont accompagnées d’une description complète.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: d75f46f8b06ef0e743218d5f42d5b2732b6e4158
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120977"
---
# <a name="xamarinios-errors"></a>Erreurs Xamarin. iOS

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: messages d’erreur mTouch

Par exemple, paramètres, environnement, outils manquants.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
  -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MT0000: Erreur inattendue-veuillez remplir un rapport de bogue à https://github.com/xamarin/xamarin-macios/issues/new

Une condition d’erreur inattendue s’est produite. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec autant d’informations que possible, notamment:

- Journaux de génération complets, avec un niveau de détail maximal `-v -v -v -v` (par exemple, dans les **arguments mTouch supplémentaires**);
- Un cas de test minimal qui reproduit l’erreur; les
- Toutes les informations sur les versions

Le moyen le plus simple d’obtenir des informations de version exactes consiste à utiliser le menu **Visual Studio pour Mac** , **à propos de Visual Studio pour Mac** élément, afficher le bouton **Détails** et copier/coller la version informations (vous pouvez utiliser le bouton **copier les informations** ) .

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001: «-devname» a été fourni sans action spécifique à l’appareil

Il s’agit d’un avertissement émis si-devname est passé à mTouch quand aucune action spécifique de l’appareil (-logdev/-installdev/-killdev/-launchdev/-listapps) n’a été demandée.

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002: Impossible d’analyser la variable d’environnement *.

Cette erreur se produit si vous tentez de définir une paire clé-valeur de variable d’environnement non valide. Le format correct est le suivant:`mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003: Le nom d’application' *. exe’est en conflit avec un kit de développement logiciel (SDK) ou un nom d’assembly de produit (. dll).

Le nom de l’assembly exécutable et le nom de l’application ne peuvent pas correspondre au nom d’une dll dans l’application. Modifiez le nom de votre fichier exécutable.

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004: La nouvelle logique refcounting nécessite l’activation de SGen.

Si vous activez l’extension refcounting, vous devez également activer le garbage collector SGen dans les options de génération iOS du projet (onglet Avancé).

À compter de Xamarin. iOS 7.2.1 cette exigence a été levée, la nouvelle logique refcounting peut être activée avec les garbage collector Boehm et SGen.

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005: Le répertoire de sortie * n’existe pas.

Créez le répertoire.

Cette erreur n’est plus générée, mTouch crée automatiquement le répertoire s’il n’existe pas.

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006: Il n’existe aucune plateforme devel sur *, use--Platform = PLAT pour spécifier le kit de développement logiciel (SDK).

Xamarin. iOS ne peut pas trouver le répertoire du kit de développement logiciel (SDK) à l’emplacement mentionné dans le message d’erreur. Vérifiez que le chemin d’accès est correct.

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007: L’assembly racine * n’existe pas.

Xamarin. iOS ne peut pas trouver l’assembly à l’emplacement mentionné dans le message d’erreur. Vérifiez que le chemin d’accès est correct.

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008: Vous devez fournir un assembly racine uniquement, # Assemblies trouvés: *.

Plus d’un assembly racine a été passé à mTouch, alors qu’il ne peut y avoir qu’un seul assembly racine.

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009: Erreur lors du chargement des assemblys: *.

Une erreur s’est produite lors du chargement des assemblys à partir des références d’assembly racine. Des informations supplémentaires peuvent être fournies dans la sortie de la génération.

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010: Impossible d’analyser les arguments de ligne de commande: *.

Une erreur s’est produite lors de l’analyse des arguments de ligne de commande. Veuillez vérifier qu’ils sont corrects.

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: * a été généré avec un Runtime plus récent (*) que le support monotactile.

Cet avertissement est généralement signalé, car le projet a une référence à une bibliothèque de classes qui n’a pas été créée à l’aide de la BCL Xamarin. iOS.

De la même façon qu’une application utilisant le kit de développement logiciel (SDK) .NET 4,0 peut ne pas fonctionner sur un système qui prend uniquement en charge .NET 2,0, une bibliothèque créée à l’aide de .NET 4,0 peut ne pas fonctionner sur Xamarin. iOS, mais elle peut utiliser l’API qui n’est pas présente sur Xamarin. iOS.

La solution générale consiste à générer la bibliothèque sous la forme d’une bibliothèque de classes Xamarin. iOS. Pour ce faire, vous pouvez créer un nouveau projet de bibliothèque de classes Xamarin. iOS et y ajouter tous les fichiers sources. Si vous n’avez pas le code source de la bibliothèque, contactez le fournisseur et demandez-lui de fournir une version compatible Xamarin. iOS de sa bibliothèque.

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012: Des données incomplètes sont fournies pour terminer *.

Cette erreur n’est plus signalée dans la version actuelle de Xamarin. iOS.

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013: La prise en charge du profilage requiert l’activation de SGen.

SGen (--Sgen) doit être activé si le profilage (--profilage) est activé.

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014: Le kit de développement logiciel (SDK) iOS * ne prend pas en charge la création d’applications ciblant *.

Cela peut se produire dans les circonstances suivantes:

- ARMv6 est activé et Xcode 4,5 ou version ultérieure est installé.
- ARMv7s est activé et Xcode 4,4 ou version antérieure est installé.

Vérifiez que la version installée de Xcode prend en charge les architectures sélectionnées.

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x86_64--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015: ABI non valide: *. Les Abi pris en charge sont: i386, x86_64, ARMv7, ARMv7 + LLVM, ARMv7 + LLVM + Thumb2, armv7s, armv7s + LLVM, armv7s + LLVM + Thumb2, arm64 et arm64 + LLVM.

Un ABI non valide a été passé à mTouch. Spécifiez un ABI valide.

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016: L’option * est dépréciée.

L’option mTouch indiquée est dépréciée et sera ignorée.

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017: Vous devez fournir un assembly racine.

Il est nécessaire de spécifier un assembly racine (en général, le fichier exécutable principal) lors de la génération d’une application.

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018: Argument de ligne de commande inconnu: *.

MTouch ne reconnaît pas l’argument de ligne de commande mentionné dans le message d’erreur.

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019: Une seule option--[log | installer | Kill | Launch] dev ou--[Launch | debug] SIM peut être utilisée.

Il existe plusieurs options pour mTouch qui ne peuvent pas être utilisées simultanément:

- --logdev
- --installdev
- --killdev
- --launchdev
- --launchdebug
- --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020: Les options valides pour\*«» sont\*«».

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021: Compilation impossible à l’aide de gcc/g + + (--Use-GCC) lors de l’utilisation du Registre statique (il s’agit de la valeur par défaut lors de la compilation pour l’appareil). Supprimez l’indicateur--Use-GCC ou utilisez le Bureau d’enregistrement dynamique (--Registrar: Dynamic).

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022: Les options «--inpris en charge--Enable-génériques-in-Registrar» et «--Registrar» ne sont pas compatibles.

Supprimez les options `--unsupported--enable-generics-in-registrar` et `--registrar`. À compter de Xamarin. iOS 7.2.1, le Bureau d’enregistrement par défaut prend en charge les génériques.

Cette erreur n’est plus affichée (l’argument `--unsupported--enable-generics-in-registrar` de ligne de commande a été supprimé de mTouch).

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023: Le nom d’application' *. exe’est en conflit avec un autre assembly utilisateur.

Le nom de l’assembly exécutable et le nom de l’application ne peuvent pas correspondre au nom d’une dll dans l’application. Modifiez le nom de votre fichier exécutable.

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024: Impossible de trouver le fichier requis' * '.

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025: Aucune version du kit de développement logiciel n’a été fournie. Veuillez ajouter `--sdk=X.Y` pour spécifier le kit de développement logiciel (SDK) iOS à utiliser pour générer votre application.

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026: Impossible d’analyser l’argument de ligne de commande' * ': *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027: Les options «\*» et «\*» ne sont pas compatibles.

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028: Impossible d’activer le graphique à secteurs (-Pie) quand vous ciblez iOS 4,1 ou une version antérieure. Désactivez le graphique à secteurs (-Pie: faux) ou définissez la cible de déploiement sur au moins iOS 4,2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029: REPL (--Enable-REPL) est pris en charge uniquement dans le simulateur (--SIM).

REPL est pris en charge uniquement si vous créez pour le simulateur. Cela signifie que si vous transmettez `--enable-repl` à mTouch, vous devez également passer. `--sim`

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030: Le nom de l’exécutable\*() et le nom de\*l’application () sont différents, ce qui peut empêcher les journaux d’incident d’être correctement plantages.

Lorsque Xcode symbolicates (traduit les adresses mémoire en noms de fonction et les numéros de fichier/ligne), le processus peut échouer si l’exécutable et l’application ont des noms différents (sans l’extension).

Pour résoudre ce problème, modifiez «nom de l’application» dans les options de l’application de build/iOS du projet ou modifiez «nom de l’assembly» dans les options de génération/sortie du projet.

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031: Les arguments de ligne de commande'--Enable-background-FETCH’et'--Launch-for-background-FETCH’requièrent également'--launchsim'.

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032: L’option'--debugtrack’est ignorée, sauf si'--Debug’est également spécifié.

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033: Un projet Xamarin. iOS doit faire référence à unitouch. dll ou Xamarin. iOS. dll

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034: Impossible d’inclure à la fois’unitouch. dll’et’Xamarin. iOS. dll’dans le même projet Xamarin.\*IOS-' 'est explicitement référencé,\*tandis que' 'est référencé par' * '.

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036: Impossible de lancer un simulateur * pour une application *. Activez la ou les architectures appropriées dans les options de génération iOS de votre projet (page avancé).

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x86_64"></a>MT0037: unitouch. dll n’est pas compatible 64 bits. Référencez Xamarin. iOS. dll ou ne générez pas pour une architecture 64 bits (ARM64 et/ou x86_64).

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038: Les anciens bureaux d’enregistrement (--Registrar: oldstatic | olddynamic) ne sont pas pris en charge lors du référencement de Xamarin. iOS. dll.

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039: Les applications ciblant ARMv6 ne peuvent pas faire référence à Xamarin. iOS. dll.

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040: Impossible de trouver l’assembly\*' ', référencé par'\*'.

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041: Impossible de référencer à la fois’unitouch. dll’et’Xamarin. iOS. dll'.

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042: Aucune référence à unitouch. dll ou Xamarin. iOS. dll n’a été trouvée. Une référence à unitouch. dll sera ajoutée.

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043: Le garbage collector Boehm n’est actuellement pas pris en charge lors du référencement de «Xamarin. iOS. dll». Le garbage collector SGen a été sélectionné à la place.

Seul le garbage collector SGen est pris en charge avec les projets unifiés. Assurez-vous qu’il n’existe aucun indicateur mTouch supplémentaire spécifiant Boehm comme garbage collector.

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044:--listsim est pris en charge uniquement avec Xcode 6,0 ou version ultérieure.

Installez une version de Xcode plus récente.

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:--l’extension est prise en charge uniquement lors de l’utilisation du kit de développement logiciel (SDK) iOS 8,0 (ou version ultérieure).

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047: La cible de déploiement minimale pour les applications unifiées est 5.1.1, la cible de déploiement actuelle est' * '. Sélectionnez une cible de déploiement plus récente dans les options de l’application iOS de votre projet.

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049: *. Framework est pris en charge uniquement si la cible de déploiement est 8,0 ou une version ultérieure. * les fonctionnalités peuvent ne pas fonctionner correctement.

L’infrastructure spécifiée n’est pas prise en charge dans la version iOS à laquelle la cible de déploiement fait référence. Mettez à jour la cible de déploiement vers une version iOS plus récente ou supprimez l’utilisation du Framework spécifié de l’application.

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051: Xamarin. iOS * requiert Xcode 5,0 ou une version ultérieure. La version actuelle de Xcode (trouvée dans *) est *.

Installez un Xcode plus récent.

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052: Aucune commande n’est spécifiée.

Aucune action n’a été spécifiée pour mTouch.

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054: Impossible de rendre canonique le chemin d’accès' * ': *

Il s’agit d’une erreur interne. Si vous voyez cette erreur, veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055: Le chemin d’accès Xcode' * 'n’existe pas.

Le chemin d’accès Xcode `--sdkroot` passé à l’aide de n’existe pas. Spécifiez un chemin d’accès valide.

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056: Xcode est introuvable à l’emplacement par défaut (/Applications/Xcode.app). Installez Xcode ou passez un chemin d’accès personnalisé à l’aide de \<--SDKRoot Path >.

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057: Impossible de déterminer le chemin d’accès à Xcode. app à partir de la racine du kit de développement logiciel (SDK) ' * '. Spécifiez le chemin d’accès complet au Bundle Xcode. app.

Le chemin d’accès `--sdkroot` passé à l’aide de ne spécifie pas d’application Xcode valide. Spécifiez un chemin d’accès à une application Xcode.

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058: Xcode. app'\*'n’est pas valide (le fichier'\*'n’existe pas).

Le chemin d’accès `--sdkroot` passé à l’aide de ne spécifie pas d’application Xcode valide. Spécifiez un chemin d’accès à une application Xcode.

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059: Impossible de trouver le Xcode actuellement sélectionné sur le système: *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060: Impossible de trouver le Xcode actuellement sélectionné sur le système. «Xcode-Select--Print-Path» a retourné «*», mais ce répertoire n’existe pas.

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061: Aucune application Xcode. app spécifiée (à l’aide de--SDKRoot) à l’aide du système Xcode comme indiqué par «Xcode-Select--Print-Path»: *

Il s’agit d’un avertissement d’information, expliquant quel Xcode sera utilisé, car aucun n’a été spécifié.

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062: Aucune application Xcode. app spécifiée (à l’aide de--SDKRoot ou de «Xcode-Select--Print-Path»), en utilisant par défaut Xcode à la place: *

Il s’agit d’un avertissement d’information, expliquant quel Xcode sera utilisé, car aucun n’a été spécifié.

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063: Impossible de trouver l’exécutable dans l’extension * (aucune entrée CFBundleExecutable dans son fichier info. plist)

Chaque information. plist doit avoir un exécutable (à l’aide de l’entrée CFBundleExecutable), mais une entrée doit être générée automatiquement pendant la génération.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064: Xamarin. iOS prend uniquement en charge les frameworks incorporés avec des projets unifiés.

Xamarin. iOS prend uniquement en charge les frameworks intégrés lors de l’utilisation de l’API unifiée; Mettez à jour votre projet pour utiliser le API unifiée.

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065: Xamarin. iOS prend uniquement en charge les frameworks intégrés lorsque la cible de déploiement est au moins 8,0 (cible de déploiement actuelle: * frameworks incorporés: *)

Xamarin. iOS prend uniquement en charge les frameworks intégrés lorsque la cible de déploiement est d’au moins 8,0 (car les versions antérieures d’iOS ne prennent pas en charge les frameworks incorporés).

Veuillez mettre à jour la cible de déploiement dans le fichier info. plist du projet sur 8,0 ou une version ultérieure.

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066: Assembly d’inscription de build non valide: *

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067: Bureau d’enregistrement non valide: *

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068: Valeur non valide pour la version cible de .NET Framework: *.

Une version cible de .NET Framework non valide a été passée à l’aide de l’argument--Target-Framework. Spécifiez un Framework cible valide.

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070: Framework cible non valide: *. Les frameworks cibles valides sont: *.

Une version cible de .NET Framework non valide a été passée à l’aide de l’argument--Target-Framework. Spécifiez un Framework cible valide.

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071: Plateforme inconnue: *. Cela indique généralement un bogue dans Xamarin. iOS. Veuillez signaler un rapport de bogue dans http://bugzilla.xamarin.com à l’aide d’un cas de test.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072: Les extensions ne sont pas prises en charge pour la plateforme' * '.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073: Xamarin. iOS * ne prend pas en charge une cible de déploiement de * (la valeur minimale est *). Sélectionnez une cible de déploiement plus récente dans le fichier info. plist de votre projet.

La cible de déploiement minimale est celle spécifiée dans le message d’erreur. Sélectionnez une cible de déploiement plus récente dans le fichier info. plist du projet.

Si la mise à jour de la cible de déploiement n’est pas possible, utilisez une version antérieure de Xamarin. iOS.

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074: Xamarin. iOS * ne prend pas en charge une cible de déploiement minimale de * (la valeur maximale est *). Sélectionnez une cible de déploiement plus ancienne dans le fichier info. plist de votre projet, ou effectuez une mise à niveau vers une version plus récente de Xamarin. iOS.

Xamarin. iOS ne prend pas en charge la définition d’une version supérieure de la cible de déploiement minimale par rapport à la version pour laquelle cette version particulière de Xamarin. iOS a été générée.

Sélectionnez une cible de déploiement minimale antérieure dans le fichier info. plist du projet ou effectuez une mise à niveau vers une version plus récente de Xamarin. iOS.

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075: Architecture' * 'non valide pour les projets *. Les architectures valides sont les suivantes: *

Une architecture non valide a été spécifiée. Vérifiez que l’architecture est valide.

<a name="MT0076" />

### <a name="mt0076-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0076: Aucune architecture spécifiée (à l’aide de l’argument--Abi). Une architecture est requise pour les projets *.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

<a name="MT0077" />

### <a name="mt0077-watchos-projects-must-be-extensions"></a>MT0077: Les projets Watchos doivent être des extensions.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

<a name="MT0078" />

### <a name="mt0078-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0078: Les builds incrémentielles sont activées avec une cible de déploiement < 8,0 (actuellement *). Cela n’est pas pris en charge (l’application résultante ne s’exécutera pas sur iOS 9). par conséquent, la cible de déploiement sera définie sur 8,0.

Il s’agit d’un avertissement qui indique que la cible de déploiement a été définie sur 8,0 pour cette build afin que les builds incrémentielles fonctionnent correctement.

Les builds incrémentielles sont prises en charge uniquement lorsque la cible de déploiement est d’au moins 8,0 (car l’application résultante ne s’exécutera pas sur iOS 9).

<a name="MT0079" />

### <a name="mt0079-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0079: La version de Xcode recommandée pour Xamarin. iOS * est Xcode * ou version ultérieure. La version actuelle de Xcode (trouvée dans *) est *.

Il s’agit d’un avertissement indiquant que la version actuelle de Xcode n’est pas la version recommandée de Xcode pour cette version de Xamarin. iOS.

Mettez à niveau Xcode pour garantir un comportement optimal.

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080: La désactivation de NewRefCount,--New-refcount: false, est déconseillée.

Il s’agit d’un avertissement indiquant que la demande de désactivation du nouveau refcount (--New-refcount: false) a été ignorée.

La nouvelle fonctionnalité refcount est maintenant obligatoire pour tous les projets et il n’est donc pas possible de la désactiver.

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081: L’argument de ligne de commande--Download-Crash-Report nécessite également--Download-crash-rapport-to.

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082: REPL (--Enable-REPL) est pris en charge uniquement lorsque la liaison n’est pas utilisée (--nolink).

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083: Le bitcode ASM uniquement n’est pas pris en charge sur Watchos. Utilisez soit--bitcode: Marker, soit--bitcode: Full.

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084: Bitcode n’est pas pris en charge dans le simulateur. Ne pas passer--bitcode lors de la génération pour le simulateur.

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085: Aucune référence à' * 'n’a été trouvée. Elle sera ajoutée automatiquement.

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086: Une version cible de .NET Framework (--Target-Framework) doit être spécifiée lors de la génération pour TVOS ou Watchos.

Cela indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087: Les builds incrémentielles (--fastdev) ne sont pas prises en charge avec le GC Boehm. Les builds incrémentielles seront désactivées.

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088: Le GC doit être en mode coopératif pour les applications Watchos. Supprimez l’argument--Coop: false dans mTouch.

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089: L’option «\*» ne peut pas prendre la\*valeur «» lorsque le mode coopératif est activé pour le gc.

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091: Cette version de Xamarin. iOS requiert le kit de développement logiciel (SDK) * (fourni avec Xcode *). Mettez à niveau Xcode pour récupérer les fichiers d’en-tête requis ou définissez le comportement de l’éditeur de liens managé sur Link Framework SDK only (pour essayer d’éviter les nouvelles API).

Xamarin. iOS requiert les fichiers d’en-tête, à partir de la version du kit de développement logiciel (SDK) spécifiée dans le message d’erreur, pour générer votre application. La méthode recommandée pour corriger cette erreur consiste à mettre à niveau Xcode afin d’extraire le kit de développement logiciel (SDK) requis. cela inclut tous les fichiers d’en-tête requis. Si vous avez installé plusieurs versions de Xcode ou si vous souhaitez utiliser un Xcode à un emplacement autre que celui par défaut, veillez à définir l’emplacement de Xcode approprié dans les préférences de votre IDE.

Une autre solution potentielle consiste à activer l’éditeur de liens managé. Cette opération supprime l’API inutilisée, y compris, dans la plupart des cas, la nouvelle API où les fichiers d’en-tête sont manquants (ou incomplets). Toutefois, cela ne fonctionnera pas si votre projet utilise une API qui a été introduite dans un SDK plus récent que celui fourni par Xcode.

Une dernière solution consiste à utiliser une version antérieure de Xamarin. iOS, qui prend en charge le kit de développement logiciel (SDK) dont votre projet a besoin.

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093: Impossible de trouver’mlaunch'.

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095: Les fichiers AOA n’ont pas pu être copiés dans le répertoire de destination {dest}: {Error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096: Aucune référence à Xamarin. iOS. dll n’a été trouvée.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099: Erreur interne *. Veuillez signaler un rapport de bogues avec un cas http://bugzilla.xamarin.com) de test (.

Ce message d’erreur est signalé en cas d’échec d’une vérification de cohérence interne dans Xamarin. iOS.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100: Cible de génération d’assembly non valide: ' * '. Veuillez signaler un rapport de bogues avec un cas http://bugzilla.xamarin.com) de test (.

Ce message d’erreur est signalé en cas d’échec d’une vérification de cohérence interne dans Xamarin. iOS.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101: L’assembly' * 'est spécifié plusieurs fois dans les arguments--assembly-Build-Target.

L’assembly mentionné dans le message d’erreur est spécifié plusieurs fois dans les arguments--assembly-Build-Target. Vérifiez que chaque assembly n’est mentionné qu’une seule fois.

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102: Les assemblys'\*'et'\*'ont le même nom cible ('\*'), mais des cibles différentes (\*' 'et' * ').

Les assemblys mentionnés dans le message d’erreur ont des cibles de génération conflictuelles.

Par exemple :

```
  --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary
```

Cet exemple tente de créer une bibliothèque dynamique et une infrastructure avec la même marque (`MyBinary`).

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103: L’objet statique «\*» contient plusieurs assemblys («\*»), mais chaque objet statique doit correspondre exactement à un assembly.

Les assemblys mentionnés dans le message d’erreur sont tous compilés en un seul objet statique. Cela n’est pas autorisé, chaque assembly doit être compilé en un objet statique différent.

Par exemple :

```
--assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary
```

Cet exemple tente de générer un objet statique (`MyBinary`) composé de deux assemblys (`Assembly1.dll` et `Assembly2.dll`), ce qui n’est pas autorisé.

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105: Aucune cible de génération d’assembly n’a été spécifiée pour' * '.

Quand vous spécifiez la cible de `--assembly-build-target`génération d’assembly à l’aide de, chaque assembly de l’application doit avoir une cible de génération assignée.

Cette erreur est signalée lorsque l’assembly mentionné dans le message d’erreur n’a pas de cible de build d’assembly assignée.

`--assembly-build-target` Pour plus d’informations, consultez la documentation.

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106: Le nom cible de la build\*de l’assembly' 'n'\*est pas valide: le caractère' 'n’est pas autorisé.

Le nom de la cible de génération de l’assembly doit être un nom de fichier valide.

Par exemple, ces valeurs déclenchent cette erreur:

```
--assembly-build-target:Assembly1.dll=staticobject=my/path.o
```

car `my/path.o` n’est pas un nom de fichier valide en raison du caractère de séparation de répertoire.

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107: Les assemblys'\*'ont des optimisations de LLVM personnalisées (\*), ce qui n’est pas autorisé lorsqu’ils sont tous compilés en un seul fichier binaire.

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108: La cible de génération de l’assembly' * 'ne correspond à aucun assembly.

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109: L’assembly{0}' 'a été chargé à partir d’un chemin d’accès différent du chemin {1}d’accès fourni ( {2}chemin d’accès fourni:, chemin d’accès réel:).

Il s’agit d’un avertissement indiquant qu’un assembly référencé par l’application a été chargé à partir d’un emplacement différent de celui qui a été demandé.

Cela peut signifier que l’application référence plusieurs assemblys portant le même nom, mais à partir de différents emplacements, ce qui peut entraîner des résultats inattendus (seul le premier assembly sera utilisé).

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110: Les builds incrémentielles ont été désactivées, car cette version de Xamarin. iOS ne prend pas en charge les builds incrémentielles dans les projets qui incluent des bibliothèques de liaison tierces et qui sont compilées en bitcode.

Les builds incrémentielles ont été désactivées, car cette version de Xamarin. iOS ne prend pas en charge les builds incrémentielles dans les projets qui incluent des bibliothèques de liaison tierces et qui sont compilées en bitcode (projets tvOS et Watchos).

Aucune action n’est requise de votre part, ce message est purement informatif.

Pour plus d’informations, consultez le bogue n °[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Cet avertissement n’est plus signalé.

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111: Bitcode a été activé, car cette version de Xamarin. iOS ne prend pas en charge la génération de projets Watchos à l’aide de LLVM sans activer Bitcode.

Bitcode a été activé automatiquement, car cette version de Xamarin. iOS ne prend pas en charge la génération de projets Watchos à l’aide de LLVM sans activer Bitcode.

Aucune action n’est requise de votre part, ce message est purement informatif.

Pour plus d’informations, consultez le bogue n °[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112: Le partage de code natif a été désactivé, car *

Il existe plusieurs raisons pour lesquelles le partage de code peut être désactivé:

- étant donné que la cible de déploiement de l’application conteneur est antérieure à iOS 8,0 (*)).

Le partage de code natif nécessite iOS 8,0 car le partage de code natif est implémenté à l’aide d’infrastructures d’utilisateur, introduites avec iOS 8,0.

- étant donné que l’application de conteneur comprend des assemblys I18N (*).

Le partage de code natif n’est actuellement pas pris en charge si l’application conteneur comprend des assemblys I18N.

- parce que l’application conteneur a des définitions XML personnalisées pour l’éditeur de liens managé (*).

Le partage de code natif requiert n’est pas pris en charge pour les projets qui utilisent des définitions XML personnalisées pour l’éditeur de liens managé.

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113: Le partage de code natif a été désactivé pour l’extension' * ', car *.

- étant donné que les options bitcode diffèrent entre l'\*application conteneur () et\*l’extension ().

  Le partage de code natif requiert que les options bitcode correspondent entre les projets qui partagent du code.

- étant donné que les options--assembly-Build-target sont différentes entre l'\*application conteneur () et\*l’extension ().

  Le partage de code natif requiert que les options--assembly-Build-Target soient identiques entre les projets qui partagent du code.

  Cette condition peut se produire si les builds incrémentielles ne sont pas activées ou désactivées dans tous les projets.

- étant donné que les assemblys i18n sont différents entre l’application\*conteneur () et l'\*extension ().

  Le partage de code natif n’est actuellement pas pris en charge pour les extensions qui incluent des assemblys I18N.

- comme les arguments du compilateur AOA sont différents entre l’application conteneur (\*) et l’extension (\*).

  Le partage de code natif requiert que les arguments du compilateur AOA ne diffèrent pas entre les projets qui partagent du code.

- étant donné que les autres arguments du compilateur AOA sont différents entre l’application conteneur\*() et l’extension\*().

  Le partage de code natif requiert que les arguments du compilateur AOA ne diffèrent pas entre les projets qui partagent du code.

  Cette condition se produit si l’opération «effectuer toutes les opérations float 32 bits en tant que valeurs float 64 bits» diffère entre les projets.

- étant donné que LLVM n’est pas activé ou désactivé à la fois\*dans l’application conteneur (\*) et l’extension ().

  Le partage de code natif requiert que LLVM soit activé ou désactivé pour tous les projets qui partagent du code.

- étant donné que les paramètres de l’éditeur de liens managé sont différents\*entre l’application conteneur (\*) et l’extension ().

  Le partage de code natif requiert que les paramètres de l’éditeur de liens managé soient identiques pour tous les projets qui partagent du code.

- étant donné que les assemblys ignorés pour l’éditeur de liens managé sont différents entre l'\*application conteneur () et\*l’extension ().

  Le partage de code natif requiert que les paramètres de l’éditeur de liens managé soient identiques pour tous les projets qui partagent du code.

- parce que l’extension a des définitions XML personnalisées pour l’éditeur de liens managé (*).

  Le partage de code natif requiert n’est pas pris en charge pour les projets qui utilisent des définitions XML personnalisées pour l’éditeur de liens managé.

- parce que l’application conteneur n’est pas générée pour l’ABI * (pendant la génération de l’extension pour cet ABI).

  Le partage de code natif requiert que l’application conteneur soit générée pour toutes les architectures pour lesquelles toute extension d’application est générée.

  Par exemple: cette condition se produit lorsqu’une extension est générée pour ARM64 + ARMv7, mais que l’application conteneur est uniquement générée pour ARM64.

- parce que l’application conteneur est en génération pour \*l’ABI, qui n’est pas compatible avec l’Abi\*de l’extension ().

  Le partage de code natif requiert que tous les projets soient générés pour la même API.

  Par exemple: cette condition se produit lorsqu’une extension est générée pour ARMv7 + LLVM + Thumb2, mais que l’application conteneur est uniquement générée pour ARMv7 + LLVM.

- étant donné que l’application de conteneur référence l'\*assembly'\*'à partir de' ', alors que l’extension fait référence à une autre version de' * '.

  Le partage de code natif requiert que tous les projets qui partagent du code utilisent les mêmes versions pour tous les assemblys.

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115: Il est recommandé de référencer des symboles dynamiques à l’aide du code (--Dynamic-Symbol-mode = Code) quand bitcode est activé.

Les projets Xamarin. iOS référencent souvent les symboles natifs de manière dynamique, ce qui signifie que l’éditeur de liens natif peut supprimer ces symboles natifs pendant le processus de liaison natif, car l’éditeur de liens natif ne voit pas que ces symboles sont utilisés.

Généralement, Xamarin. iOS demande à l’éditeur de liens natif de conserver ces symboles ( `-u symbol` à l’aide de l’indicateur de l’éditeur de liens), mais lors de la compilation `-u` pour bitcode, l’éditeur de liens natif n’accepte pas l’indicateur.

Xamarin. iOS a implémenté une solution alternative: nous générons du code natif supplémentaire qui fait référence à ces symboles. par conséquent, l’éditeur de liens natif verra que ces symboles sont utilisés. Cette opération est effectuée automatiquement lors de la compilation sur bitcode.

Si `--dynamic-symbol-mode=linker` est passé à mTouch, cette solution alternative est désactivée et Xamarin. iOS tente de passer `-u` à l’éditeur de liens natif. Cela provoquera très probablement des erreurs natives de l’éditeur de liens.

La solution consiste à supprimer l' `--dynamic-symbol-mode=linker` argument des arguments mTouch supplémentaires dans les options de génération du projet.

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116: Architecture non valide: {Arch}. les architectures 32 bits ne sont pas prises en charge lorsque le déploiement cible est 11 ou une version ultérieure. Assurez-vous que le projet n’est pas généré pour une architecture 32 bits.

iOS 11 ne contient pas de prise en charge pour les applications 32 bits. il n’est donc pas possible de générer une application 32 bits lorsque la cible de déploiement est iOS 11 ou une version ultérieure.

Remplacez l’architecture cible des options de génération iOS du projet par arm64 ou remplacez la cible de déploiement dans le fichier info. plist du projet par une version iOS antérieure.

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117: Impossible de lancer une application 32 bits sur un simulateur qui prend uniquement en charge 64 bits.

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118: Les fichiers AOA sont introuvables dans le répertoire attendu «{msymdir}».

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123: L’assembly exécutable * ne fait pas référence à *.

Aucune référence à l’assembly de plateforme (Xamarin. iOS. dll/Xamarin. TVOS. dll/Xamarin. Watchos. dll) n’a été trouvée dans l’assembly exécutable.

Cela se produit généralement quand aucun code dans le projet exécutable n’utilise rien de l’assembly de plateforme. par exemple, une méthode main vide (et aucun autre code) affiche l’erreur suivante:

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

L’utilisation d’une API à partir de l’assembly de plateforme permet de résoudre l’erreur:

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124: Impossible de définir la langue actuelle sur «{lang}» (en fonction de LANG = {LANG}): {exception}

Il s’agit d’un avertissement indiquant que la langue actuelle n’a pas pu être définie sur la langue du message d’erreur.

La langue actuelle est par défaut la langue du système.

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125: L’argument de ligne de commande--assembly-Build-target est ignoré dans le simulateur.

Aucune action n’est requise, ce message est purement informatif.

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126: Les builds incrémentielles ont été désactivées, car les builds incrémentielles ne sont pas prises en charge dans le simulateur.

Aucune action n’est requise, ce message est purement informatif.

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127: Les builds incrémentielles ont été désactivées, car cette version de Xamarin. iOS ne prend pas en charge les builds incrémentielles dans les projets qui incluent plus d’une bibliothèque de liaisons tierces.

Les builds incrémentielles ont été désactivées automatiquement, car cette version de Xamarin. iOS ne génère pas toujours des projets avec plusieurs bibliothèques de liaisons tierces correctement.

Aucune action n’est requise, ce message est purement informatif.

Pour plus d’informations, consultez le bogue n °[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128: Impossible de toucher au fichier' * ': *

Une erreur s’est produite lors du contact d’un fichier (qui est effectuée pour garantir que les builds partielles sont effectuées correctement).

Cet avertissement peut probablement être ignoré; en cas de problème, envoyez un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) et il sera examiné.

<a name="MT0135" />

### <a name="mt0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MT0135: L’infrastructure{0}système' 'n’a pas été liée (référencée par l’assembly'{1}') {2} , car elle a été introduite dans {3}et nous utilisons le {2} {4} Kit de développement logiciel (SDK).

Pour générer votre application, Xamarin. iOS doit être lié à des bibliothèques système, dont certaines dépendent de la version du kit de développement logiciel (SDK) spécifiée dans le message d’erreur. Étant donné que vous utilisez une version antérieure du kit de développement logiciel (SDK), les appels à ces API peuvent échouer au moment de l’exécution.

La méthode recommandée pour corriger cette erreur consiste à mettre à niveau Xcode pour récupérer le kit de développement logiciel (SDK) nécessaire. Si plusieurs versions de Xcode sont installées ou si vous souhaitez utiliser un Xcode à un emplacement autre que celui par défaut, veillez à définir l’emplacement Xcode correct dans les préférences de votre IDE.

Vous pouvez également activer l' [éditeur de liens](https://docs.microsoft.com/en-us/xamarin/ios/deploy-test/linker) managé pour supprimer les API inutilisées, y compris (dans la plupart des cas) celles qui nécessitent la bibliothèque spécifiée. Toutefois, cela ne fonctionnera pas si votre projet nécessite des API introduites dans un SDK plus récent que celui fourni par Xcode.

En tant que solution de dernière paille, utilisez une version antérieure de Xamarin. iOS qui ne requiert pas que ces nouveaux kits de développement logiciel (SDK) soient présents pendant le processus de génération.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Messages d’erreur liés au projet

### <a name="mt10xx-installer--mtouch"></a>MT10xx: Programme d’installation/mTouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001: Impossible de trouver une application dans le répertoire spécifié

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002: Impossible de créer liens symboliques, les fichiers ont été copiés

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003: Impossible de supprimer l’application' * '. Vous devrez peut-être arrêter l’application manuellement.

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004: Impossible d’accéder à la liste des applications installées.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Messages d’erreur liés au projet

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005: Impossible de supprimer l’application'\*'sur l’appareil'\*': *-vous devrez peut-être arrêter l’application manuellement.

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006: Impossible d’installer l’application «\*» sur l’appareil «\*»: *.

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007: Échec du lancement de l’application\*' 'sur l’appareil\*' ': *. Vous pouvez toujours lancer l’application manuellement en appuyant dessus.

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008: Échec du lancement du simulateur

Cette erreur est signalée si mTouch n’a pas pu lancer le simulateur.   Cela peut se produire parfois lorsqu’un processus simulateur obsolète ou inactif est déjà en cours d’exécution.

La commande suivante émise sur la ligne de commande UNIX peut être utilisée pour supprimer les processus du simulateur bloqué:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009: Impossible de copier l’assembly\*' 'dans\*' ': *

Il s’agit d’un problème connu dans certaines versions de Xamarin. iOS.

Si cela se produit, essayez la solution de contournement suivante:

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

Toutefois, étant donné que ce problème a été résolu dans la dernière version de Xamarin. iOS, veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec vos informations de version complète et la sortie du journal de génération.

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010: Impossible de charger l’assembly' * ': *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011: Impossible d’ajouter le fichier de ressources manquant: ' * '

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012: Échec de la liste des applications sur l’appareil' * ': *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013: Erreur de suivi des dépendances: aucun fichier à comparer. Veuillez signaler un rapport de bogue dans http://bugzilla.xamarin.com à l’aide d’un cas de test.

Cela indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014: Échec de la réutilisation de la version mise en cache de' * ': *.

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Impossible de créer le fichier exécutable' * ': *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Impossible de créer le fichier exécutable' * ': *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016: Impossible de créer le fichier d’avis, car un répertoire portant le même nom existe déjà.

Supprimez le `NOTICE` répertoire du projet.

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017: Échec de la création du fichier d’avis: *.

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018: Votre application n’a pas pu vérifier la signature du code et n’a pas pu être installée sur l’appareil' * '. Vérifiez vos certificats, les profils de provisionnement et les ID de bundle. Votre appareil ne fait probablement pas partie du profil d’approvisionnement sélectionné (erreur: 0xe8008015).

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019: Votre application a des droits non pris en charge par votre profil d’approvisionnement actuel et n’a pas pu être installé sur l’appareil' * '. Consultez le journal de l’appareil iOS pour obtenir des informations plus détaillées (erreur: 0xe8008016).

Cela peut se produire si :

- Votre application a des droits que le profil de provisionnement actuel ne prend pas en charge.
  Solutions possibles :
  - Spécifiez un autre profil de provisionnement qui prend en charge les droits dont votre application a besoin.
  - Supprimez les droits non pris en charge dans le profil de configuration actuel.
- L’appareil sur lequel vous essayez d’effectuer le déploiement n’est pas inclus dans le profil de provisionnement que vous utilisez.
  Solutions possibles :
  - Créez une application à partir d’un modèle dans Xcode, sélectionnez le même profil de provisionnement et déployez sur le même appareil. Xcode peut parfois actualiser automatiquement les profils de provisionnement avec de nouveaux appareils (dans d’autres cas, Xcode vous demande quoi faire).
  -Accédez au centre de développement iOS et mettez à jour le profil de provisionnement avec le nouvel appareil, puis téléchargez le profil d’approvisionnement mis à jour sur votre ordinateur.

Dans la plupart des cas, des informations supplémentaires sur l’échec sont imprimées dans le journal de l’appareil iOS, ce qui peut aider à diagnostiquer le problème.

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020: Échec du lancement de l’application\*' 'sur l’appareil\*' ': *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021: Impossible de copier le fichier'\*'dans'\*':{2}

Un fichier n’a pas pu être copié. Le message d’erreur de l’opération de copie contient plus d’informations sur l’erreur.

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022: Impossible de copier le répertoire «\*» dans «\*»:{2}

Un répertoire n’a pas pu être copié. Le message d’erreur de l’opération de copie contient plus d’informations sur l’erreur.

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023: Impossible de communiquer avec l’appareil pour trouver l’application' * ': *

Une erreur s’est produite lors de la tentative de recherche d’une application sur l’appareil.

Voici ce que vous pouvez tenter de résoudre:

- Supprimez l’application de l’appareil et réessayez.
- Déconnectez l’appareil et reconnectez-le.
- Redémarrez l’appareil.
- Redémarrez le Mac.

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024: Impossible de vérifier la signature de l’application sur l’appareil' * '. Assurez-vous que le profil de provisionnement est installé et qu’il n’a pas expiré (erreur: 0xe8008017).

L’appareil a refusé l’installation de l’application, car la signature n’a pas pu être vérifiée.

Assurez-vous que le profil de provisionnement est installé et qu’il n’a pas expiré.

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025: Impossible de répertorier les rapports d’incidents sur l’appareil *.

Une erreur s’est produite lors de la tentative de liste des rapports d’incidents sur l’appareil.

Voici ce que vous pouvez tenter de résoudre:

- Supprimez l’application de l’appareil et réessayez.
- Déconnectez l’appareil et reconnectez-le.
- Redémarrez l’appareil.
- Redémarrez le Mac.
- Synchroniser l’appareil avec iTunes (cette opération supprime tous les rapports d’incidents de l’appareil).

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026: Impossible de télécharger le rapport d’incident * à partir de l’appareil *.

Une erreur s’est produite lors de la tentative de téléchargement des rapports d’incident à partir de l’appareil.

Voici ce que vous pouvez tenter de résoudre:

- Supprimez l’application de l’appareil et réessayez.
- Déconnectez l’appareil et reconnectez-le.
- Redémarrez l’appareil.
- Redémarrez le Mac.
- Synchroniser l’appareil avec iTunes (cette opération supprime tous les rapports d’incidents de l’appareil).

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027: Impossible d’utiliser Xcode 7 + pour lancer des applications sur des appareils avec iOS * (Xcode 7 ne prend en charge que iOS 6 +).

Il n’est pas possible d’utiliser Xcode 7 + pour lancer des applications sur des appareils avec la version d’iOS inférieure à 6,0.

Utilisez une version antérieure de Xcode ou appuyez sur l’application manuellement pour la lancer.

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028: Spécification d’appareil non valide: ' * '. 'IOS', 'Watchos’ou’all’attendu.

La spécification de l’appareil passée à l’aide de--l’appareil n’est pas valide. Les valeurs valides sont: «iOS», «Watchos» ou «All».

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029: Impossible de trouver une application dans le répertoire spécifié: *

Le chemin d’accès de l’application passé à--launchdev n’existe pas. Spécifiez un bundle d’applications valide.

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030: Le lancement d’applications sur un appareil à l’aide d’un identificateur de Bundle est déconseillé. Veuillez transmettre le chemin d’accès complet au Bundle à lancer.

Il est recommandé de transmettre le chemin d’accès à l’application à lancer sur l’appareil au lieu de simplement l’ID d’offre groupée.

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031: Impossible de lancer l’application «\*» sur l’appareil «\*», car l’appareil est verrouillé. Déverrouillez l’appareil et réessayez.

Déverrouillez l’appareil et réessayez.

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032: Cet exécutable d’application est peut-être trop volumineux (* Mo) à exécuter sur l’appareil. Si bitcode a été activé, vous souhaiterez peut-être le désactiver pour le développement, il n’est nécessaire que pour soumettre des applications à Apple.

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033: Impossible de désinstaller l’application\*«» de l’appareil\*«»: *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035: Impossible d’inclure des versions différentes de l’infrastructure «{Name}»

Il n’est pas possible d’établir une liaison avec différentes versions du même Framework.

Cela se produit généralement lorsqu’une extension fait référence à une version différente d’une infrastructure que l’application conteneur (éventuellement par le biais d’un assembly de liaison tiers).

Après cette erreur, plusieurs erreurs de [MT1036](#MT1036) répertorient les chemins d’accès pour chaque infrastructure différente.

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036: Framework «{Name}» inclus dans: {Path} (lié à l’erreur précédente)

Cette erreur est signalée uniquement avec [MT1036](#MT1036). Pour plus d’informations, consultez [MT1036](#MT1036) .

### <a name="mt11xx-debug-service"></a>MT11xx: Service de débogage

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101: Impossible de démarrer l’application

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102: Impossible d’attacher à l’application (pour la supprimer): *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103: Impossible de détacher

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104: Échec de l’envoi du paquet: *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105: Type de réponse inattendu

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106: Impossible d’accéder à la liste des applications sur l’appareil: Délai d’attente de la demande dépassé.

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107: Échec du lancement de l’application: *

Vérifiez que votre appareil est verrouillé.

Si vous déployez une application d’entreprise ou à l’aide d’un profil d’approvisionnement gratuit, vous pouvez faire confiance au développeur (cette procédure est expliquée <a href="https://stackoverflow.com/a/30726375/183422">ici</a>).

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108: Impossible de trouver les outils de développement pour cet appareil XX (AA).

Quelques opérations de mTouch requièrent que `DeveloperDiskImage.dmg` le fichier soit présent.   Ce fichier fait partie de Xcode et se trouve généralement par rapport au kit de développement logiciel (SDK) que vous utilisez pour `Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg`la génération, dans le.

Cette erreur peut se produire soit parce que vous n’avez pas de DeveloperDiskImage. dmg qui correspond à l’appareil que vous avez connecté.

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109: Échec du lancement de l’application, car l’appareil est verrouillé. Déverrouillez l’appareil et réessayez.

Vérifiez que votre appareil est verrouillé.

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110: Échec du lancement de l’application en raison de restrictions de sécurité iOS. Vérifiez que le développeur est approuvé.

Si vous déployez une application d’entreprise ou à l’aide d’un profil d’approvisionnement gratuit, vous pouvez faire confiance au développeur (cette procédure est expliquée <a href="https://stackoverflow.com/a/30726375/183422">ici</a>).

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111: L’application a été lancée correctement, mais il n’est pas possible d’attendre que l’application se termine comme demandé, car il n’est pas possible de détecter l’arrêt de l’application lors de son lancement à l’aide de gdbserver.

### <a name="mt12xx-simulator"></a>MT12xx: Simulateur

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201: Impossible de charger le simulateur: *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202: Configuration de simulateur non valide: *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203: Spécification de simulateur non valide: *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204: Spécification de simulateur non valide' * ': Runtime non spécifié.

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205: Spécification de simulateur non valide' * ': le type de périphérique n’est pas spécifié.

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206: Impossible de trouver le runtime du simulateur' * '.

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207: Impossible de trouver le type d’appareil du simulateur' * '.

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208: Impossible de trouver le runtime du simulateur' * '.

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209: Impossible de trouver le type d’appareil du simulateur' * '.

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210: Spécification de simulateur \*non valide:,\*clé inconnue' '

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211: La version du simulateur «\*» ne prend pas en charge le type de simulateur «»\*

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212: Échec de la création d’une version de simulateur où type = * et Runtime = *.

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213: Spécification de simulateur non valide pour XCode 4: *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214: Spécification de simulateur non valide pour XCode 5: *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215: Kit de développement logiciel (SDK) non valide spécifié: *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216: Impossible de trouver le UDID du simulateur' * '.

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217: Impossible de charger le bundle d’applications à l’adresse' * '.

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218: Aucun identificateur de Bundle n’a été trouvé dans l’application à l’adresse' * '.

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219: Impossible de trouver le simulateur pour' * '.

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220: Le dernier Runtime du simulateur est introuvable pour l’appareil' * '.

Cela indique généralement un problème avec Xcode.

Voici ce que vous pouvez tenter de résoudre:

- Utilisez le simulateur une seule fois dans Xcode.
- Passer une version explicite du SDK à l’aide \<de--SDK version >.
- Réinstallez Xcode.

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221: Impossible de trouver le simulateur iPhone apparié pour le simulateur Watchos' * '.

Lors du lancement d’une application Watchos dans un simulateur Watchos, il doit également exister un simulateur iOS couplé.

Les simulateurs de Watch peuvent être associés à des simulateurs iOS à l’aide de l’interface utilisateur appareils de Xcode (fenêtre de menus-> appareils).

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301: La bibliothèque `*` native\*() a été ignorée, car elle ne correspond pas à la ou aux\*architectures de build actuelles ()

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302: Impossible d’extraire la bibliothèque Native' * 'à partir de' + '. Vérifiez que la bibliothèque native a été correctement incorporée dans l’assembly managé (si l’assembly a été généré à l’aide d’un projet de liaison, la bibliothèque Native doit être incluse dans le projet, et son action de génération doit être’ObjcBindingNativeLibrary').

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303: Impossible de décompresser le Framework natif\*' 'à\*partir de' '. Consultez le journal de génération pour plus d’informations à partir de la commande «zip» native.

Impossible de décompresser l’infrastructure Native spécifiée à partir de la bibliothèque de liaisons.

Pour plus d’informations sur cet échec, consultez le journal bulid à partir de la commande «zip» native.

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304: Le Framework incorporé' * 'dans * n’est pas valide: il ne contient pas d’informations. plist.

L’infrastructure incorporée spécifiée ne contient pas d’info. plist et n’est donc pas une infrastructure valide.

Assurez-vous que le Framework est valide.

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305: La bibliothèque de liaisons\*«» contient une infrastructure utilisateur\*(), mais les infrastructures utilisateur incorporées requièrent iOS 8,0 (la cible de déploiement actuelle est *). Définissez la cible de déploiement dans le fichier info. plist sur au moins 8,0.

La bibliothèque de liaisons spécifiée contient une infrastructure intégrée, mais Xamarin. iOS prend uniquement en charge les frameworks intégrés sur iOS 8,0 ou version ultérieure.

Définissez la cible de déploiement dans le fichier info. plist sur au moins 8,0 pour résoudre cette erreur (ou n’utilisez pas les frameworks intégrés).

### <a name="mt14xx-crash-reports"></a>MT14xx: Rapports d’incidents

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400: Impossible d’ouvrir le service de rapport d’incident: AFCConnectionOpen retourné *

Une erreur s’est produite lors de la tentative d’accès aux rapports d’incident à partir de l’appareil.

Voici ce que vous pouvez tenter de résoudre:

- Supprimez l’application de l’appareil et réessayez.
- Déconnectez l’appareil et reconnectez-le.
- Redémarrez l’appareil.
- Redémarrez le Mac.
- Synchroniser l’appareil avec iTunes (cette opération supprime tous les rapports d’incidents de l’appareil).

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401: Impossible de fermer le service de rapport d’incident: AFCConnectionClose retourné *

Une erreur s’est produite lors de la tentative d’accès aux rapports d’incident à partir de l’appareil.

Voici ce que vous pouvez tenter de résoudre:

- Supprimez l’application de l’appareil et réessayez.
- Déconnectez l’appareil et reconnectez-le.
- Redémarrez l’appareil.
- Redémarrez le Mac.
- Synchroniser l’appareil avec iTunes (cette opération supprime tous les rapports d’incidents de l’appareil).

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402: Impossible de lire les informations du fichier pour *: AFCFileInfoOpen retourné *

Une erreur s’est produite lors de la tentative d’accès aux rapports d’incident à partir de l’appareil.

Voici ce que vous pouvez tenter de résoudre:

- Supprimez l’application de l’appareil et réessayez.
- Déconnectez l’appareil et reconnectez-le.
- Redémarrez l’appareil.
- Redémarrez le Mac.
- Synchroniser l’appareil avec iTunes (cette opération supprime tous les rapports d’incidents de l’appareil).

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403: Impossible de lire le rapport d’incident: AFCDirectoryOpen (*) retourné: *

Une erreur s’est produite lors de la tentative d’accès aux rapports d’incident à partir de l’appareil.

Voici ce que vous pouvez tenter de résoudre:

- Supprimez l’application de l’appareil et réessayez.
- Déconnectez l’appareil et reconnectez-le.
- Redémarrez l’appareil.
- Redémarrez le Mac.
- Synchroniser l’appareil avec iTunes (cette opération supprime tous les rapports d’incidents de l’appareil).

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404: Impossible de lire le rapport d’incident: AFCFileRefOpen (*) retourné: *

Une erreur s’est produite lors de la tentative d’accès aux rapports d’incident à partir de l’appareil.

Voici ce que vous pouvez tenter de résoudre:

- Supprimez l’application de l’appareil et réessayez.
- Déconnectez l’appareil et reconnectez-le.
- Redémarrez l’appareil.
- Redémarrez le Mac.
- Synchroniser l’appareil avec iTunes (cette opération supprime tous les rapports d’incidents de l’appareil).

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405: Impossible de lire le rapport d’incident: AFCFileRefRead (*) retourné: *

Une erreur s’est produite lors de la tentative d’accès aux rapports d’incident à partir de l’appareil.

Voici ce que vous pouvez tenter de résoudre:

- Supprimez l’application de l’appareil et réessayez.
- Déconnectez l’appareil et reconnectez-le.
- Redémarrez l’appareil.
- Redémarrez le Mac.
- Synchroniser l’appareil avec iTunes (cette opération supprime tous les rapports d’incidents de l’appareil).

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406: Impossible de répertorier les rapports d’incident: AFCDirectoryOpen (*) retourné: *

Une erreur s’est produite lors de la tentative d’accès aux rapports d’incident à partir de l’appareil.

Voici ce que vous pouvez tenter de résoudre:

- Supprimez l’application de l’appareil et réessayez.
- Déconnectez l’appareil et reconnectez-le.
- Redémarrez l’appareil.
- Redémarrez le Mac.
- Synchroniser l’appareil avec iTunes (cette opération supprime tous les rapports d’incidents de l’appareil).

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600: N’est pas une bibliothèque dynamique Mach-O (en-tête inconnu «0x *»): *.

Une erreur s’est produite lors du traitement de la bibliothèque dynamique en question.

Vérifiez que la bibliothèque dynamique est une bibliothèque dynamique Mach-O valide.

Le format d’une bibliothèque peut être vérifié à l' `file` aide de la commande à partir d’un terminal:

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601: N’est pas une bibliothèque statique (en-tête inconnu' * '): *.

Une erreur s’est produite lors du traitement de la bibliothèque statique en question.

Vérifiez que la bibliothèque statique est une bibliothèque statique Mach-O valide.

Le format d’une bibliothèque peut être vérifié à l' `file` aide de la commande à partir d’un terminal:

```
file -arch all -l /path/to/library.a
```

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602: N’est pas une bibliothèque dynamique Mach-O (en-tête inconnu «0x *»): *.

Une erreur s’est produite lors du traitement de la bibliothèque dynamique en question.

Vérifiez que la bibliothèque dynamique est une bibliothèque dynamique Mach-O valide.

Le format d’une bibliothèque peut être vérifié à l' `file` aide de la commande à partir d’un terminal:

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603: Format inconnu pour l’entrée FAT à la position * dans *.

Une erreur s’est produite lors du traitement de l’archive FAT en question.

Assurez-vous que l’archive FAT est valide.

Le format d’une archive Fat peut être vérifié à l' `file` aide de la commande à partir d’un terminal:

```
file -arch all -l /path/to/file
```

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604: Le fichier de type * n’est pas un fichier MachO (*).

Une erreur s’est produite lors du traitement du fichier MachO en question.

Assurez-vous que le fichier est une bibliothèque dynamique Mach-O valide.

Le format d’un fichier peut être vérifié à l' `file` aide de la commande à partir d’un terminal:

```
file -arch all -l /path/to/file
```

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx: Messages d’erreur de l’éditeur de liens

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001: Impossible de lier les assemblys

Cette erreur signifie que l’éditeur de liens managé a rencontré une erreur inattendue, par exemple une exception, et n’a pas pu terminer ou enregistrer l’assembly en cours de traitement. Plus d’informations sur l’erreur exacte feront partie du journal de génération, par exemple

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

Il est important de signaler un rapport de bogue pour de tels problèmes. Dans la plupart des cas, une solution de contournement peut être fournie jusqu’à la publication d’un correctif approprié. Les informations ci-dessus sont critiques (avec un cas de test et/ou l’assembly binairy) pour résoudre le problème.

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002: Impossible de résoudre la référence: *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003: L’option' * 'sera ignorée, car la liaison est désactivée

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004: Le fichier de définitions de l’éditeur de liens supplémentaire' * 'est introuvable.

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005: Impossible d’analyser les définitions de' * '.

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006: Impossible de charger mscorlib. dll à partir de: *. Réinstallez Xamarin. iOS.

Cela indique généralement qu’il y a un problème avec votre installation de Xamarin. iOS. Essayez de réinstaller Xamarin. iOS.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010: HttpMessageHandler `*`inconnu. Les valeurs valides sont HttpClientHandler (par défaut), CFNetworkHandler ou NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011: TlsProvider `*`inconnu.  Les valeurs valides sont default ou appletls.

La valeur donnée à `tls-provider=` n’est pas un fournisseur TLS (Transport Layer Security) valide.

Les `default` et`appletls` sont les seules valeurs valides, et les deux représentent la même option, qui consiste à fournir la prise en charge SSL/TLS à l’aide de l’API native d’Apple TLS.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015: HttpMessageHandler `*` non valide pour Watchos. La seule valeur valide est NSUrlSessionHandler.

Il s’agit d’un avertissement qui se produit car le fichier projet spécifie un HttpMessageHandler non valide.

Les versions antérieures de nos outils d’aperçu généraient par défaut une valeur non valide dans le fichier projet.

Pour résoudre cet avertissement, ouvrez le fichier projet dans un éditeur de texte, puis supprimez tous les nœuds HttpMessageHandler du fichier XML.

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016: Option TlsProvider `legacy` non valide. La seule valeur `appletls` valide sera utilisée.

Le `legacy` fournisseur, qui était un fournisseur SSLv3/TLSv1 entièrement géré, n’est plus livré avec Xamarin. iOS. Les projets qui utilisaient cet ancien fournisseur et sont désormais générés `appletls` avec le plus récent.

Pour résoudre cet avertissement, ouvrez le fichier projet dans un éditeur de texte, puis supprimez tous les nœuds «MtouchTlsProvider» du code XML.

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017: Impossible de traiter la description XML.

Cela signifie qu’il y a une erreur dans le fichier de configuration de l' [éditeur de liens XML personnalisé](~/cross-platform/deploy-test/linker.md) que vous avez fourni, veuillez vérifier votre fichier.

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018: L’assembly\*' 'est référencé à partir de deux emplacements différents\*: ' 'et' * '.

L’assembly mentionné dans le message d’erreur est chargé à partir de plusieurs emplacements. Veillez à toujours utiliser la même version d’un assembly.

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019: Impossible de charger l’assembly racine' * '

L’assembly racine n’a pas pu être chargé. Vérifiez que le chemin d’accès figurant dans le message d’erreur fait référence à un fichier existant et qu’il s’agit d’un assembly .NET valide.

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x: Le traitement `...`de l’optimiseur de liaison a échoué.

Une erreur inattendue s’est produite lors de la tentative d’optimisation du code de liaison généré. L’élément à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly nommé (ou contenant le type ou la méthode nommé) doit être fourni dans un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un journal de génération complet avec des commentaires activés ( `-v -v -v -v` c’est-à-dire dans les **arguments mTouch supplémentaires**).

Le dernier chiffre `x` sera:
- `0`pour un nom d’assembly;
- `1`pour un nom de type;
- `3`pour un nom de méthode;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030: Échec du traitement `...`de la suppression des ressources utilisateur.

Une erreur inattendue s’est produite lors de la tentative de suppression des ressources utilisateur. L’assembly à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly doit être fourni dans un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés `-v -v -v -v` (par exemple, dans les **arguments mTouch supplémentaires**).

Les ressources utilisateur sont des fichiers inclus dans des assemblys (en tant que ressources) qui doivent être extraits au moment de la génération pour créer l’offre groupée d’applications. notamment :

- `__monotouch_content_*`et `__monotouch_pages_*` ressources; et
- Bibliothèques natives incorporées dans un assembly de liaison;

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040: Échec du traitement `...`par défaut de l’accesseur Set HttpMessageHandler.

Une erreur inattendue s’est produite lors `HttpMessageHandler` de la tentative de définition de la valeur par défaut de l’application. Signalez un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés `-v -v -v -v` (par exemple, dans les **arguments mTouch supplémentaires**).

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050: Le traitement `...`du suppression du code a échoué.

Une erreur inattendue s’est produite lors de la tentative de suppression du code de la bibliothèque BCL avec l’application. Signalez un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés `-v -v -v -v` (par exemple, dans les **arguments mTouch supplémentaires**).

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060: Échec du traitement `...`du scellement.

Une erreur inattendue s’est produite lors de la tentative de scellage des types ou des méthodes (final) ou lors de la dévirtualisation de certaines méthodes. L’assembly à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly doit être fourni dans un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés `-v -v -v -v` (par exemple, dans les **arguments mTouch supplémentaires**).

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070: Échec du traitement `...`du réducteur de métadonnées.

Une erreur inattendue s’est produite lors de la tentative de réduction des métadonnées de l’application. L’assembly à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly doit être fourni dans un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés `-v -v -v -v` (par exemple, dans les **arguments mTouch supplémentaires**).

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080: Échec du traitement `...`de MarkNSObjects.

Une erreur inattendue s’est `NSObject` produite lors de la tentative de marquage des sous-classes de l’application. L’assembly à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly doit être fourni dans un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés `-v -v -v -v` (par exemple, dans les **arguments mTouch supplémentaires**).

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090: Le traitement `...`de Inline a échoué.

Une erreur inattendue s’est produite lors de la tentative de code en ligne à partir de l’application. L’assembly à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly doit être fourni dans un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés ( `-v -v -v -v` par exemple, dans les **arguments mTouch supplémentaires**).

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100: Échec du traitement `...`du Préserveur de conversion d’énumération Smart Enum.

Une erreur inattendue s’est produite lors de la tentative de marquage des méthodes de conversion pour les énumérations intelligentes à partir de l’application. L’assembly à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly doit être fourni dans un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés ( `-v -v -v -v` par exemple, dans les **arguments mTouch supplémentaires**).

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101: Impossible de résoudre la référence\*' ', référencée à partir de\*la méthode' 'dans' * '.

Une référence d’assembly non valide a été rencontrée lors du traitement de la méthode mentionnée dans le message d’erreur.

L’assembly à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly doit être fourni dans un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés `-v -v -v -v` (par exemple, dans les **arguments mTouch supplémentaires**).

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102: Erreur lors du traitement de\*la méthode' 'dans\*l’assembly' ': *

Une erreur inattendue s’est produite lors de la tentative de marquage de la méthode mentionnée dans le message d’erreur.

L’assembly à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly doit être fourni dans un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés `-v -v -v -v` (par exemple, dans les **arguments mTouch supplémentaires**).

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103: Erreur lors du traitement\*de l’assembly' ': *

Une erreur inattendue s’est produite lors du traitement d’un assembly.

L’assembly à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly doit être fourni dans un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , ainsi qu’un journal de génération complet avec des commentaires activés `-v -v -v -v` (par exemple, dans les **arguments mTouch supplémentaires**).

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: Impossible de lier l’assembly'{0}', car il est en mode mixte.

Les assemblys en mode mixte ne peuvent pas être traités par l’éditeur de liens.

Pour https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies plus d’informations sur les assemblys en mode mixte, consultez.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: Messages d’erreur AOA

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001: Impossible d’AOA l’assembly' * '

Cela indique généralement un bogue dans le compilateur AOA. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un projet qui peut être utilisé pour reproduire l’erreur.

Il est parfois possible de contourner ce problème en désactivant les builds incrémentielles dans l’option de build iOS du projet (mais il s’agit toujours d’un bogue, veuillez le signaler quand même).

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-developerxamarincomguidesiosadvanced_topicslimitationsreverse_callbacksiosinternalslimitationsmdreverse-callbacks"></a>MT3002: Restriction AOA: La méthode' * 'doit être statique, car elle est décorée avec [MonoPInvokeCallback]. Voir [developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](~/ios/internals/limitations.md#reverse-callbacks)

Ce message d’erreur provient du compilateur AOA.

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003: Les options--Debug et--LLVM sont en conflit. Le débogage réversible est désactivé.

Le débogage n’est pas pris en charge lorsque LLVM est activé. Si vous avez besoin de déboguer l’application, désactivez d’abord LLVM.

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004: Impossible d’AOA l’assembly' * ', car il n’existe pas.

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005: La dépendance «\*» de l’assembly\*«» est introuvable. Vérifiez les références du projet.

Cela se produit généralement lorsqu’un assembly fait référence à une autre version d’un assembly de plateforme (généralement la version .NET 4 de mscorlib. dll).

Cela n’est pas pris en charge et peut ne pas être généré ou exécuté correctement (l’assembly peut utiliser l’API de la version .NET 4 de mscorlib. dll que la version de Xamarin. iOS n’a pas).

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006: Impossible de calculer un mappage de dépendance complet pour le projet. Cela se traduira par des temps de génération plus lents, car Xamarin. iOS ne peut pas détecter correctement ce qui doit être reconstruit (et ce qui n’a pas besoin d’être régénéré). Pour plus d’informations, consultez les avertissements précédents.

 générer ou exécuter correctement (l’assembly peut utiliser l’API de la version .NET 4 de mscorlib. dll que la version de Xamarin. iOS n’a pas).

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007: Les fichiers d’informations de débogage (*. mdb) ne seront pas chargés quand LLVM est activé.

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008: La prise en charge de Bitcode nécessite l’utilisation du backend LLVM (--LLVM)

La prise en charge de Bitcode nécessite l’utilisation du backend LLVM (--LLVM).

Désactivez la prise en charge de Bitcode ou activez LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx: Messages d’erreur de génération de code

### <a name="mt40xx-main"></a>MT40xx: Principal

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001: Impossible d’étendre le modèle principal à `*`.

Une erreur s’est produite `main.m`lors de la génération. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002: Échec de la compilation du code généré pour les méthodes P/Invoke. Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

Échec de la compilation du code généré pour les méthodes P/Invoke. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

### <a name="mt41xx-registrar"></a>MT41xx: Inscription

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101: Le Bureau d’enregistrement ne peut pas générer `*`de signature pour le type.

Un type a été trouvé dans l’API exportée que le runtime ne sait pas comment marshaler vers/à partir de Objective-C.

Si vous pensez que Xamarin. iOS doit prendre en charge le type en question, veuillez saisir une demande d’amélioration sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102: Le Bureau d’enregistrement a trouvé `*` un type non valide `*`dans la signature de la méthode. Utilisez plutôt `*`.

Cela se produit actuellement uniquement avec un type: System. DateTime. Utilisez à la place l’équivalent objective-C (NSDate).

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103: Le Bureau d’enregistrement a trouvé `*` un type non valide `*`dans la signature de la méthode: Le type implémente INativeObject, mais il n’a pas de constructeur qui accepte deux arguments (IntPtr, bool)

Cela se produit lorsque le Bureau d’enregistrement rencontre un type dans une signature avec les caractéristiques mentionnées. Le Bureau d’enregistrement peut avoir besoin de créer de nouvelles instances du type et, dans ce cas, il requiert un constructeur avec la signature (IntPtr, bool)-le premier argument (IntPtr) spécifie le handle managé, tandis que le second si l’appelant passe la propriété du natif handle (si cette valeur est false, 'retain’sera appelé sur l’objet).

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104: Le Bureau d’enregistrement ne peut pas marshaler `*` la valeur de retour `*`pour le type dans la signature de la méthode.

Un type a été trouvé dans l’API exportée que le runtime ne sait pas comment marshaler vers/à partir de Objective-C.

Si vous pensez que Xamarin. iOS doit prendre en charge le type en question, veuillez saisir une demande d’amélioration sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105: Le Bureau d’enregistrement ne peut pas marshaler le paramètre de `*`type `*` dans la signature de la méthode.

Si vous pensez que Xamarin. iOS doit prendre en charge le type en question, veuillez saisir une demande d’amélioration sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106: Le Bureau d’enregistrement ne peut pas marshaler `*` la valeur de retour `*`de la structure dans la signature de la méthode.

Un type a été trouvé dans l’API exportée que le runtime ne sait pas comment marshaler vers/à partir de Objective-C.

Si vous pensez que Xamarin. iOS doit prendre en charge le type en question, veuillez saisir une demande d’amélioration sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107: Le Bureau d’enregistrement ne peut pas marshaler le paramètre de `+`type `*` dans la signature de la méthode.

Un type a été trouvé dans l’API exportée que le runtime ne sait pas comment marshaler vers/à partir de Objective-C.

Si vous pensez que Xamarin. iOS doit prendre en charge le type en question, veuillez saisir une demande d’amélioration sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108: Le Bureau d’enregistrement ne peut pas obtenir le type `*`ObjectiveC pour le type managé.

Un type a été trouvé dans l’API exportée que le runtime ne sait pas comment marshaler vers/à partir de Objective-C.

Si vous pensez que Xamarin. iOS doit prendre en charge le type en question, veuillez saisir une demande d’amélioration sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109: Échec de la compilation du code d’Registrar généré. Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

Échec de la compilation du code généré pour le Bureau d’enregistrement. Le journal de génération contiendra la sortie du compilateur natif, en expliquant pourquoi le code n’est pas compilé.

Il s’agit toujours d’un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec votre projet ou un cas de test.

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: Le Bureau d’enregistrement ne peut pas marshaler `*` le paramètre out de `*`type dans la signature de la méthode.

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111: Le Bureau d’enregistrement ne peut pas générer `*` de signature `*`pour le type dans la méthode.

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvanced_topicsregistrariosinternalsregistrarmd-for-more-information"></a>MT4112: Le Bureau d’enregistrement a trouvé `*`un type non valide. L’inscription de types génériques avec Objective-C n’est pas prise en charge et peut entraîner un comportement aléatoire et/ou des pannes (pour la compatibilité descendante avec les versions antérieures de Xamarin. iOS, `--unsupported--enable-generics-in-registrar` il est possible d’ignorer cette erreur en passant comme un mTouch supplémentaire argument dans la page des options de génération iOS du projet. Pour plus d’informations, consultez [Developer.xamarin.com/guides/iOS/advanced_topics/Registrar](~/ios/internals/registrar.md) .

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113: Le Bureau d’enregistrement a trouvé une méthode\*générique\*: '. '. L’exportation de méthodes génériques n’est pas prise en charge et entraîne des comportements aléatoires et/ou des blocages.

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114: Erreur inattendue dans le Bureau d’enregistrement\*de\*la méthode'. '-Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116: Impossible d’inscrire l’assembly' * ': *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117: Le Bureau d’enregistrement a détecté une incompatibilité de signature dans la méthode' *.* '-le sélecteur indique que la méthode accepte les * paramètres, tandis que la méthode managée a des * paramètres.

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118: Impossible d’inscrire deux types managés\*(' '\*et' ') avec le même nom natif (' * ').

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119: Impossible d’enregistrer le sélecteur «\*» du membre «\*. *», car le sélecteur est déjà inscrit sur un autre membre.

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120: Le Bureau d’enregistrement a trouvé un type\*de champ inconnu «\*» dans le champ «. *». Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

Cette erreur indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121: Impossible d’utiliser GCC/G + + pour compiler le code généré à partir du Bureau d’enregistrement statique lors de l’utilisation de l’infrastructure de comptes (les fichiers d’en-tête fournis par Apple utilisés pendant la compilation nécessitent Clang). Utilisez Clang (--compiler: Clang) ou le Bureau d’enregistrement dynamique (--Registrar: Dynamic).

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122: Impossible d’utiliser le compilateur Clang fourni dans le *.* SDK pour compiler le code généré à partir du Bureau d’enregistrement statique lorsque des noms de types non-ASCII (' * ') sont présents dans l’application. Utilisez GCC/G + + (--compiler: GCC | G + +), le Bureau d’enregistrement dynamique (--Registrar: Dynamic) ou un kit de développement logiciel (SDK) plus récent.

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123: Le type du paramètre variadiques dans la fonction variadiques' * 'doit être System. IntPtr.

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124: Non valide * trouvé sur' * '. Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

Cette erreur indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125: Le Bureau d’enregistrement a trouvé un\*type non valide «» dans\*la signature de la méthode «»: L’interface doit avoir un attribut de protocole spécifiant son type de wrapper.

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126: Impossible d’inscrire deux protocoles managés\*(' '\*et' ') avec le même nom natif (' * ').

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127: Impossible d’inscrire plus d’une méthode d’interface pour la\*méthode' ' (qui implémente'\*').

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128: Le Bureau d’enregistrement a trouvé un type de\*paramètre générique non valide'\*'dans la méthode' '. Le paramètre générique doit avoir une contrainte’NSObject'.

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129: Le Bureau d’enregistrement a trouvé un type de\*retour générique non valide'\*'dans la méthode' '. Le type de retour générique doit avoir une contrainte’NSObject'.

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: Le Bureau d’enregistrement ne peut pas exporter les méthodes statiques dans les classes génériques (' * ').

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: Le Bureau d’enregistrement ne peut pas exporter les propriétés statiques\*dans les classes génériques ('\*. ').

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132: Le Bureau d’enregistrement a trouvé un type de\*retour générique non valide'\*'dans la propriété' '. Le type de retour doit avoir une contrainte’NSObject'.

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133: Impossible de construire une instance du type' * 'à partir de Objective-C, car le type est générique. [Exception Runtime]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134: Votre application utilise le Framework «*», qui n’est pas inclus dans le kit de développement logiciel (SDK) iOS que vous utilisez pour créer votre application (cette infrastructure a été introduite dans iOS *, pendant que vous créez avec le kit de développement logiciel (SDK) iOS *). Veuillez sélectionner un nouveau kit de développement logiciel (SDK) dans les options de build iOS de votre application.

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135: Le membre'\*.\*'a un attribut d’exportation qui ne spécifie pas de sélecteur. Un sélecteur est requis.

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136: Le Bureau d’enregistrement ne peut pas marshaler le type\*de paramètre «» du paramètre «\*\*» dans la méthode «. *»

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138: Le Bureau d’enregistrement ne peut pas marshaler le type de propriété\*'\*'de la propriété'. * '.

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139: Le Bureau d’enregistrement ne peut pas marshaler le type de propriété\*'\*'de la propriété'. * '. Les propriétés avec l’attribut [Connect] doivent avoir un type de propriété NSObject (ou une sous-classe de NSObject).

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140: Le Bureau d’enregistrement a détecté une incompatibilité de signature dans la méthode' *.* '-le sélecteur indique que la méthode variadiques accepte les * paramètres, tandis que la méthode managée a des * paramètres.

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141: Impossible d’enregistrer le sélecteur\*«» sur le membre\*«», car Xamarin. iOS inscrit implicitement ce sélecteur.

Cela se produit lors de la sous-classe d’un type d’infrastructure, et lors de la tentative d’implémentation d’une méthode’retain', 'Release’ou’dealloc':

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

Il est toutefois possible de substituer ces méthodes si la classe n’est pas la première sous-classe du type Framework:

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

Dans ce cas, Xamarin. iOS `retain`remplace, `release` et `dealloc` sur la `MyNSObject` classe, et il n’y a aucun conflit.

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142: Échec de l’enregistrement du type' * '.

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143: La classe ObjectiveC' * 'n’a pas pu être inscrite, elle ne semble pas dériver de la classe ObjectiveC connue (y compris NSObject).

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144: Impossible d’inscrire la méthode' * ', car elle n’a pas de trampoline associé. Veuillez renseigner un rapport de http://bugzilla.xamarin.com bogue à l’adresse.

Cela indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145: Enum' * 'non valide: les enums avec l’attribut [Native] doivent avoir un type enum sous-jacent’long’ou’ulong'.

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146: Le paramètre Name de l’attribut Registrar sur la classe'\*' ('\*') contient un caractère non valide:\*' '\*().

Le nom d’une classe Objectice-C ne peut pas contenir d’espace blanc, `Register` ce qui signifie que l’attribut sur la classe `Name` managée correspondante ne peut pas contenir d’espace blanc pour les deux classes.

Vérifiez que l' `Register` attribut sur la classe managée mentionné dans le message d’erreur ne contient pas d’espace blanc.

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147: Détection d’un protocole héritant du protocole JSExport lors de l’utilisation du Bureau d’enregistrement dynamique. Il n’est pas possible d’exporter des protocoles vers JavaScriptCore de manière dynamique; le Bureau d’enregistrement statique doit être utilisé (ajoutez'--Registrar: static aux arguments mTouch supplémentaires dans les options de génération iOS du projet pour sélectionner le registraire statique).

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148: Le Bureau d’enregistrement a trouvé un protocole générique: ' * '. L’exportation de protocoles génériques n’est pas prise en charge.

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149: Impossible d’enregistrer la méthode\*'\*. ', car le type du premier paramètre (\*' ') ne correspond pas au type de catégorie\*(' ').

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150: Impossible d’enregistrer le type\*' ', car la propriété de\*type (' ') dans son attribut Category n’hérite pas de NSObject.

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151: Impossible d’enregistrer le type' * ', car la propriété de type de son attribut Category n’est pas définie.

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152: Impossible d’enregistrer le type' * 'en tant que catégorie parce qu’il implémente INativeObject ou sous-classe NSObject.

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153: Impossible d’enregistrer le type\*' 'en tant que catégorie, car il est générique.

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154: Impossible d’enregistrer la méthode\*' 'comme méthode de catégorie, car elle est générique.

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155: Impossible d’enregistrer la méthode\*«» avec le sélecteur\*«» en tant que méthode de catégorie sur «*», car objective-C a déjà une implémentation pour ce sélecteur.

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156: Impossible d’inscrire deux catégories (\*' 'et\*' ') avec le même nom natif (' * ').

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157: Impossible d’enregistrer la méthode de\*catégorie' ', car au moins un paramètre est requis (et son type doit correspondre au\*type de catégorie' ')

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158: Impossible d’enregistrer le constructeur * dans la catégorie *, car les constructeurs dans les catégories ne sont pas pris en charge.

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159: Impossible d’inscrire la méthode' * 'comme méthode de catégorie, car les méthodes de catégorie doivent être statiques.

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160: Caractère non valide\*«»\*() trouvé dans le\*sélecteur «»\*pour «».

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: Le Bureau d’enregistrement a trouvé une structure non\*prise en charge' ': Tous les champs d’une structure doivent également être des structures (\*le champ' '{2}avec le type' 'n’est pas une structure).

Le Bureau d’enregistrement a trouvé une structure avec des champs non pris en charge.

Tous les champs d’une structure qui est exposée à Objective-C doivent également être des structures (et non des classes).

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162: Le type'\*' (utilisé comme * {2}) n’est pas disponible dans * * (il a été introduit dans *\* *) veuillez le générer avec un nouveau kit de développement logiciel (SDK) * (généralement effectué à l’aide de la version la plus récente de Xcode.

Le Bureau d’enregistrement a trouvé un type qui n’est pas inclus dans le kit de développement logiciel (SDK) actuel.

Mettez à niveau Xcode.

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163: Erreur interne dans le Bureau d’enregistrement (*). Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

Cette erreur indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164: Impossible d’exporter la propriété\*«», car son\*sélecteur «» est un mot clé objective-C. Veuillez utiliser un autre nom.

Le sélecteur de la propriété en question n’est pas un identificateur objective-C valide.

Utilisez un identificateur objective-C valide comme sélecteurs.

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165: Le Bureau d’enregistrement n’a pas trouvé le type’System. void’dans l’un des assemblys référencés.

Cette erreur indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166: Impossible d’enregistrer la méthode\*' ', car la signature contient un\*type () qui n’est pas un type référence.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167: Impossible d’enregistrer la méthode\*' ', car la signature contient un type\*générique () avec un type d’argument générique qui n’est pas une sous-classe NSObject (*).

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managed_name-because-its-objective-c-name-exported_name-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168: Impossible d’enregistrer le type «{\_Managed Name}», car son nom objective\_-c «{Export Name}» est un mot clé objective-c. Veuillez utiliser un autre nom.

Le nom objective-C du type en question n’est pas un identificateur objective-C valide.

Utilisez un identificateur objective-C valide.

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169: Échec de la génération d’un wrapper P/Invoke pour {Method}: {message}

Xamarin. iOS n’a pas pu générer une fonction wrapper P/Invoke pour le mentionné.
Vérifiez le message d’erreur signalé pour la cause sous-jacente.

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170: Le Bureau d’enregistrement ne peut pas convertir «{type managé}» en «{type natif}» pour la valeur de retour dans la méthode {Method}.

Consultez la description de l’erreur <a href="#MT4172">MT4172</a>.

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171: L’attribut Bindas sur le membre {member} n’est pas valide: le type de Liaisonas {type} est différent du type de propriété {type}.

Assurez-vous que le type dans l’attribut Bindas correspond au type du membre auquel il est attaché.

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172: Le Bureau d’enregistrement ne peut pas convertir «{Native type}» en «{type managé}» pour le paramètre «{Name}» dans la méthode {Method}.

Le Bureau d’enregistrement ne prend pas en charge la conversion entre les types mentionnés.

Il s’agit d’un bogue dans Xamarin. iOS si l’API en question est fournie par Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Si vous rencontrez ce code lors du développement d’un projet de liaison pour une bibliothèque native, nous sommes ouverts à l’ajout de la prise en charge de nouvelles combinaisons de types. Si c’est le cas, veuillez faire une demande d’amélioration sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test et nous l’évaluerons.

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: Messages d’erreur GCC et chaîne d’outils

### <a name="mt51xx-compilation"></a>MT51xx: Compilation

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101: Compilateur' * 'manquant. Installez le composant outils en ligne de commande Xcode

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102: Impossible d’assembler le fichier' * '. Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103: Échec de la compilation du fichier' * '. Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104: Impossible de trouver le\*compilateur' 'ou'\*'. Installez le composant outils en ligne de commande Xcode

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106: Impossible de compiler le ou les fichiers' * '. Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

### <a name="mt52xx-linking"></a>MT52xx: Liaison

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201: Échec de la liaison native. Consultez le journal de génération et les indicateurs utilisateur fournis à gcc: *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202: Échec de la liaison native. Consultez le journal de génération.

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203: Avertissement de liaison Native: *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209: Erreur de liaison Native: *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210: Échec de la liaison native, symbole non défini: *. Vérifiez que toutes les infrastructures nécessaires ont été référencées et que les bibliothèques natives sont correctement liées.

Cela se produit lorsque l’éditeur de liens natif ne trouve pas de symbole référencé quelque part. Cela peut se produire pour plusieurs raisons:

- Une liaison tierce requiert une infrastructure, mais la liaison ne la spécifie pas dans son `[LinkWith]` attribut. Elles
  - Si vous êtes l’auteur de la liaison tierce ou si vous avez accès à sa source, modifiez l’attribut de `[LinkWith]` la liaison pour inclure l’infrastructure dont elle a besoin:

    ```csharp
    [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]
    ```

  - Si vous ne pouvez pas modifier la liaison tierce, vous pouvez établir une liaison manuelle avec l’infrastructure requise `-gcc_flags '-framework SystemFramework'` en `mtouch` passant à (cette opération s’effectue en modifiant les arguments mTouch supplémentaires dans la page des options de génération iOS du projet. N’oubliez pas que cela doit être fait pour chaque configuration de projet).
- Dans certains cas, une liaison managée est composée de plusieurs bibliothèques natives, et toutes doivent être incluses dans les liaisons. Il est possible d’avoir plusieurs bibliothèques natives dans chaque projet de liaison. par conséquent, la solution consiste simplement à ajouter toutes les bibliothèques natives requises au projet de liaison.</li>
- Une liaison managée fait référence à des symboles natifs qui n’existent pas dans la bibliothèque native.
    Cela se produit généralement lorsqu’une liaison a existé pendant un certain temps et que le code natif a été modifié pendant ce temps afin qu’une classe Native particulière soit supprimée ou renommée, alors que la liaison n’a pas été mise à jour.
- Un appel P/Invoke fait référence à un symbole natif qui n’existe pas. À compter de Xamarin. iOS 7,4, une erreur <a href="#MT5214">MT5214</a> est signalée pour ce cas (pour plus d’informations, consultez MT5214).
- Une liaison/bibliothèque tierce a été créée à l' C++aide de, mais la liaison ne la spécifie `[LinkWith]` pas dans son attribut. C’est généralement relativement facile à reconnaître, car les symboles sont décomposés C++ de symboles (un exemple `__ZNKSt9exception4whatEv`courant est).
  - Si vous êtes l’auteur de la liaison tierce ou si vous avez accès à sa source, modifiez l’attribut de `[LinkWith]` la liaison pour définir l' `IsCxx` indicateur:

    ```csharp
    [LinkWith ("mylib.a", IsCxx = true)]
    ```

  - Si vous ne pouvez pas modifier la liaison tierce ou si vous effectuez une liaison manuelle avec une bibliothèque tierce, vous pouvez définir l’indicateur équivalent en passant `-cxx` à mTouch (cette opération s’effectue en modifiant les arguments mTouch supplémentaires dans la page Options de génération iOS du projet . N’oubliez pas que cela doit être fait pour chaque configuration de projet).

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211: Échec de la liaison native, classe objective-C non \*définie:. Le symbole «\*» est introuvable dans les bibliothèques ou les infrastructures liées à votre application.

Cela se produit lorsque l’éditeur de liens natif ne peut pas trouver une classe objective-C référencée quelque part. Cela peut se produire pour plusieurs raisons: comme pour [MT5210](#MT5210) et en plus:

- Une liaison tierce liée à un protocole objective-C, mais ne l’a pas annotée `[Protocol]` avec l’attribut dans sa définition d’API. Elles
  - Ajoutez l’attribut `[Protocol]` manquant:

    ```csharp
    [BaseType (typeof (NSObject))]
    [Protocol] // Add this
    public interface MyProtocol
    {
    }
    ```

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212: Échec de la liaison native, symbole dupliqué: *.

Cela se produit lorsque l’éditeur de liens natif rencontre des symboles dupliqués entre toutes les bibliothèques natives. Après cette erreur, il peut y avoir une ou plusieurs erreurs [MT5213](#MT5213) avec l’emplacement pour chaque occurrence du symbole. Raisons possibles pour cette erreur:

- La même bibliothèque native est incluse deux fois.
- Deux bibliothèques natives distinctes se produisent pour définir les mêmes symboles.
- Une bibliothèque Native n’est pas générée correctement et contient le même symbole plusieurs fois.
  Vous pouvez le vérifier à l’aide de l’ensemble de commandes suivant à partir d’un terminal (remplacez i386 par x86_64/ARMv7/armv7s/arm64 en fonction de l’architecture que vous générez pour):

  ```
  # Native libraries are usually fat libraries, containing binary code for
  # several architectures in the same file. First we extract the binary
  # code for the architecture we're interested in.
  lipo libNative.a -thin i386 -output libNative.i386.a

  # Now query the native library for the duplicated symbol.
  nm libNative.i386.a | fgrep 'SYMBOL'

  # You can also list the object files inside the native library.
  # In most cases this will reveal duplicated object files.
  ar -t libNative.i386.a
  ```

  Il existe plusieurs façons de résoudre ce problème:

  - Demandez que le fournisseur de la bibliothèque Native le corrige et fournisse la version mise à jour.
  - Résolvez-le vous-même en supprimant les fichiers objets supplémentaires (cela fonctionne uniquement si le problème est lié à des fichiers objets dupliqués)

  ```
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
  ```

  - Demandez à l’éditeur de liens de supprimer le code inutilisé. Xamarin. iOS effectue cette opération automatiquement si toutes les conditions suivantes sont remplies:
    - Tous les `[LinkWith]` attributs des liaisons tierces ont activé a:

      ```csharp
      [assembly: LinkWith ("libNative.a", SmartLink = true)]
      ```

    - Aucun `-gcc_flags` n’est passé à mTouch (dans le champ arguments mTouch supplémentaires des options de génération iOS du projet).
    - Il est également possible de demander directement à l’éditeur de liens de supprimer le code inutilisé en ajoutant `-gcc_flags -dead_strip` aux arguments mTouch supplémentaires dans les options de génération iOS du projet.

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213: Symbole dupliqué dans: * (emplacement lié à l’erreur précédente)

Cette erreur est signalée uniquement avec [MT5212](#MT5212). Pour plus d’informations, consultez [MT5212](#MT5212) .

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214: Échec de la liaison native, symbole non défini: *. Ce symbole a fait référence au membre managé *. Vérifiez que toutes les infrastructures nécessaires ont été référencées et que les bibliothèques natives sont liées.

Cette erreur est signalée lorsque le code managé contient un P/Invoke pour une méthode native qui n’existe pas. Par exemple :

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Voici quelques solutions possibles:

- Supprimez les P/Invoke en question du code source.
- Activez l’éditeur de liens managé pour tous les assemblys (cette opération s’effectue dans les options de génération iOS du projet, en définissant «comportement de l’éditeur de liens» sur «tous les assemblys»). Cela a pour effet de supprimer tous les P/Invoke que vous n’utilisez pas de l’application (automatiquement, et non pas manuellement comme le point précédent). L’inconvénient est que cela rendra la génération de votre simuleur un peu plus lente et risque de perturber votre application si elle utilise la réflexion. vous trouverez des informations supplémentaires sur l’éditeur de liens [ici](~/ios/deploy-test/linker.md) .
- Créez une deuxième bibliothèque native qui contient les symboles natifs manquants. Notez qu’il s’agit simplement d’une solution de contournement (si vous tentez d’appeler ces fonctions, votre application se bloque).

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215: Les références à' * 'peuvent nécessiter des instructions-Framework supplémentaires = XXX ou-lXXX pour l’éditeur de liens natif

Il s’agit d’un avertissement indiquant qu’un appel P/Invoke a été détecté pour référencer la bibliothèque en question, mais que l’application ne se lie pas à lui.

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216: Échec de la liaison native pour *. Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

Cette erreur est signalée lors de la liaison de la sortie du compilateur AOA.

Cette erreur indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217: La liaison native a peut-être échoué, car la ligne de commande de l’éditeur de liens était trop longue (* caractères).

Échec de la liaison native. cela est peut-être dû au fait que la commande de l’éditeur de liens était trop longue.

Les projets Xamarin. iOS référencent souvent les symboles natifs de manière dynamique, ce qui signifie que l’éditeur de liens natif peut supprimer ces symboles natifs pendant le processus de liaison natif, car l’éditeur de liens natif ne voit pas que ces symboles sont utilisés.

Généralement, Xamarin. iOS demande à l’éditeur de liens natif de conserver ces symboles `-u symbol` à l’aide de l’indicateur de l’éditeur de liens, mais s’il existe de nombreux symboles de ce type, l’ensemble de la ligne de commande peut dépasser la longueur maximale de la ligne de commande, comme spécifié par le système d’exploitation.

Il existe plusieurs sources possibles pour ces symboles dynamiques:

- P/Invoke des méthodes dans les bibliothèques liées statiquement (où le nom de la `__Internal` dll se trouve dans `[DllImport ("__Internal")]`l’attribut DllImport).
- Références de champs aux emplacements de mémoire dans les bibliothèques liées statiquement à`[Field]` partir des projets de liaison (attributs).
- Classes objective-C référencées dans des bibliothèques liées de manière statique à partir de projets de liaison (en cas d’utilisation de builds incrémentielles ou lorsque le registre statique n’est pas utilisé).

Solutions possibles :

- Activez l’éditeur de liens managé (si possible pour tous les assemblys au lieu de uniquement les assemblys SDK). Cela peut supprimer suffisamment de sources pour les symboles dynamiques afin que la ligne de commande de l’éditeur de liens ne dépasse pas la valeur maximale.
- Réduisez le nombre d’appels P/Invoke, de références de champ et/ou de classes objective-C.
- Réécrivez les symboles dynamiques avec des noms plus courts.
- Transmettez `-dlsym:false` comme argument mTouch supplémentaire dans les options de génération iOS du projet. Avec cette option, Xamarin. iOS génère une référence native dans le code compilé par l’AOA et n’a pas besoin de demander à l’éditeur de liens de conserver ce symbole. Toutefois, cela ne fonctionne que pour les builds d’appareils et entraîne des erreurs de l’éditeur de liens s’il y a P/Invoke vers des fonctions qui n’existent pas dans la bibliothèque statique.
- Transmettez `--dynamic-symbol-mode=code` comme arguments mTouch supplémentaires dans les options de génération iOS du projet. Avec cette option, Xamarin. iOS génère du code natif supplémentaire qui référence ces symboles au lieu de demander à l’éditeur de liens natif de conserver ces symboles à l’aide d’arguments de ligne de commande. L’inconvénient de cette approche est qu’elle augmente un peu la taille de l’exécutable.
- Activez le registre statique en passant `--registrar:static` comme argument mTouch supplémentaire dans les options de génération iOS du projet (pour les builds du simulateur, puisque le Bureau d’enregistrement statique est déjà utilisé par défaut pour les builds d’appareils). Le Bureau d’enregistrement statique génère du code qui référence les classes objective-C de manière statique. il n’est donc pas nécessaire de demander à l’éditeur de liens natif de conserver ces classes.
- Désactivez les builds incrémentielles (pour les builds d’appareils). Lorsque les builds incrémentielles sont activées, le code généré par l’registraire statique n’est pas pris en compte par l’éditeur de liens natif, ce qui signifie que Xamarin. iOS doit toujours demander à l’éditeur de liens de conserver les classes objective-C référencées. Ainsi, la désactivation des builds incrémentielles empêchera ce besoin.

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218: Impossible d’ignorer le symbole dynamique {Symbol} (--ignore-Dynamic-Symbol = {Symbol}), car il n’a pas été détecté en tant que symbole dynamique.

L’argument `--ignore-dynamic-symbol=symbol` de ligne de commande a été passé, mais ce symbole n’est pas un symbole qui a été reconnu comme un symbole dynamique qui doit être préservé manuellement.

Il y a deux raisons principales à cela:

- Le nom du symbole est incorrect.
  - N’ajoutez pas de trait de soulignement au nom du symbole.
  - Le symbole des classes objective-C `OBJC_CLASS_$_<classname>`est.
- Le symbole est correct, mais il s’agit d’un symbole déjà préservé par des moyens normaux (certaines options de génération provoquent la liste exacte des symboles dynamiques).

### <a name="mt53xx-other-tools"></a>MT53xx: Autres outils

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301: Outil «Strip» manquant. Installez le composant outils en ligne de commande Xcode

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302: Outil’dsymutil’manquant. Installez le composant outils en ligne de commande Xcode

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303: Échec de la génération des symboles de débogage (répertoire dSYM). Consultez le journal de génération.

Une erreur s’est produite lors de l’exécution de dsymutil sur le répertoire final. app pour créer les symboles de débogage. Consultez le journal de génération pour voir la sortie de dsymutil et voir comment elle peut être corrigée.

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304: Impossible de supprimer le fichier binaire final. Consultez le journal de génération.

Une erreur s’est produite lors de l’exécution de l’outil «Strip» pour supprimer les informations de débogage de l’application.

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305: Outil’LiPo’manquant. Installez le composant outils en ligne de commande Xcode

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306: Échec de la création de la bibliothèque FAT. Consultez le journal de génération.

Une erreur s’est produite lors de l’exécution de l’outil «LiPo». Vérifiez le journal de génération pour voir l’erreur signalée par «LiPo».

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307: Échec de la signature de l’exécutable. Consultez le journal de génération.

Une erreur s’est produite lors de la signature de l’application. Vérifiez le journal de génération pour voir l’erreur signalée par «Codesign».

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: messages d’erreur des outils internes mTouch

### <a name="mt600x-stripper"></a>MT600x: Reformater

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001: L’exécution de la version de Cecil ne prend pas en charge la suppression d’assembly

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002: Impossible de supprimer l' `*`assembly.

Une erreur s’est produite lors de la suppression du code managé (suppression du code IL) des assemblys de l’application.

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [UnauthorizedAccessException message]

Une erreur de sécurité s’est produite lors de la suppression des symboles de débogage de l’application.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: Messages d’erreur MSBuild

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001: Impossible de résoudre les adresses IP de l’hôte pour les paramètres du débogueur WiFi.

*Tâche MSBuild: DetectDebugNetworkConfigurationTaskBase*

Étapes de dépannage:

- essayez d’exécuter `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (qui devrait vous fournir une adresse IP et non une erreur évidemment).
- essayez d’exécuter «ping \`hostname\`», qui peut vous fournir plus d’informations, par exemple:`cannot resolve MyHost.local: Unknown host`

Dans certains cas, il s’agit d’un problème de «réseau local» qui peut être résolu en ajoutant l' `127.0.0.1   MyHost.local` hôte `/etc/hosts`inconnu dans.

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002: Cet ordinateur n’a pas de carte réseau. Cela est nécessaire lors du débogage ou du profilage sur un appareil via WiFi.

*Tâche MSBuild: DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003: L’extension d’application' * 'ne contient pas de fichier info. plist.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004: L’extension d’application' * 'ne spécifie pas de CFBundleIdentifier.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005: L’extension d’application' * 'ne spécifie pas de CFBundleExecutable.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006: L’extension d’application\*' 'a un CFBundleIdentifier non\*valide (), elle ne commence pas par le CFBundleIdentifier (*) du bundle d’applications principal.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007: L’extension d’application\*' 'a un CFBundleIdentifier\*() qui se termine par le suffixe non conforme ". Key".

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008: L’extension d’application' * 'ne spécifie pas de CFBundleShortVersionString.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009: L’extension d’application' * 'a un fichier info. plist non valide: elle ne contient pas de dictionnaire NSExtension.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010: L’extension d’application' * 'comporte un fichier info. plist non valide: le dictionnaire NSExtension ne contient pas de valeur NSExtensionPointIdentifier.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011: L’extension WatchKit' * 'a un fichier info. plist non valide: le dictionnaire NSExtension ne contient pas de dictionnaire NSExtensionAttributes.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012: L’extension WatchKit' * 'n’a pas exactement une application Watch.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013: L’extension WatchKit' * 'a un fichier info. plist non valide: UIRequiredDeviceCapabilities doit contenir la fonctionnalité «Watch-Companion».

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014: L’application Watch' * 'ne contient pas de fichier info. plist.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015: L’application Watch' * 'ne spécifie pas de CFBundleIdentifier.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016: L’application Watch'\*'a un CFBundleIdentifier non valide\*(), elle ne commence pas par le CFBundleIdentifier (*) du bundle d’applications principal.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017: L’application Watch «\*» n’a pas de valeur UIDeviceFamily valide. 'Watch (4) 'attendu, mais'\* (*) 'trouvé.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018: L’application Watch' * 'ne spécifie pas de CFBundleExecutable

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019: L’application Watch «\*» a une valeur WKCompanionAppBundleIdentifier non valide (\*«»), elle ne correspond pas à la CFBundleIdentifier («*») du bundle d’applications principal.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020: L’application Watch' * 'possède un fichier info. plist non valide: la clé WKWatchKitApp doit être présente et avoir la valeur’true'.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021: L’application Watch' * 'a un fichier info. plist non valide: la clé LSRequiresIPhoneOS ne doit pas être présente.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022: L’application Watch' * 'ne contient pas d’extension Watch.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023: L’extension Watch' * 'ne contient pas de fichier info. plist.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024: L’extension Watch' * 'ne spécifie pas de CFBundleIdentifier.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025: L’extension Watch' * 'ne spécifie pas de CFBundleExecutable.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026: L’extension Watch'\*'a un CFBundleIdentifier non valide\*(), elle ne commence pas par le CFBundleIdentifier (*) du bundle d’applications principal.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027: L’extension Watch «\*» a un CFBundleIdentifier (\*) qui se termine par le suffixe non conforme «. Key».

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028: L’extension Watch «*» a un fichier info. plist non valide: elle ne contient pas de dictionnaire NSExtension.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029: L’extension Watch' * 'a un fichier info. plist non valide: le NSExtensionPointIdentifier doit être "com. Apple. watchkit".

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030: L’extension Watch' * 'a un fichier info. plist non valide: le dictionnaire NSExtension doit contenir NSExtensionAttributes.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031: L’extension Watch' * 'a un fichier info. plist non valide: le dictionnaire NSExtensionAttributes doit contenir un WKAppBundleIdentifier.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032: L’extension WatchKit' * 'a un fichier info. plist non valide: UIRequiredDeviceCapabilities ne doit pas contenir la fonctionnalité «Watch-Companion».

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033: L’application Watch' * 'ne contient pas de fichier info. plist.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034: L’application Watch' * 'ne spécifie pas de CFBundleIdentifier.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035: L’application Watch «\*» n’a pas de valeur UIDeviceFamily valide. '\*'Attendu, mais'\* (\*) 'trouvé.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036: L’application Watch' * 'ne spécifie pas de CFBundleExecutable.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037: L’extension WatchKit «{ExtensionName}» a une valeur WKAppBundleIdentifier non valide (\*«»), elle ne correspond pas à la CFBundleIdentifier de l’application\*espion («»).

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038: L’application Watch «*» a un fichier info. plist non valide: le WKCompanionAppBundleIdentifier doit exister et doit correspondre au CFBundleIdentifier du bundle d’applications principal.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039: L’application Watch' * 'a un fichier info. plist non valide: la clé LSRequiresIPhoneOS ne doit pas être présente.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040: L’offre groupée d’applications {AppBundlePath} ne contient pas de fichier info. plist.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041: Le chemin d’accès principal info. plist ne spécifie pas de CFBundleIdentifier.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042: Le chemin d’accès principal info. plist ne spécifie pas de CFBundleExecutable.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043: Le chemin d’accès principal info. plist ne spécifie pas de CFBundleSupportedPlatforms.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044: Le chemin d’accès principal info. plist ne spécifie pas de UIDeviceFamily.

*Tâche MSBuild: ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045: Format non reconnu: *.

*Tâche MSBuild: PropertyListEditorTaskBase*

Où * peut être:

- string
- array
- dict
- bool
- real
- integer
- date
- data

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046: Ajouter Entrée, *, spécifiée de manière incorrecte.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047: Ajouter L’entrée, *, contient un index de tableau non valide.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048: Add: * l’entrée existe déjà.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049: Ajouter Impossible d’ajouter l’entrée, *, au parent.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050: Supprimer : Impossible de supprimer l’entrée, *, du parent.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051: Supprimer : L’entrée, *, contient un index de tableau non valide.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052: Supprimer : L’entrée, *, n’existe pas.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053: Port Entrée, *, spécifiée de manière incorrecte.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054: Port L’entrée, *, contient un index de tableau non valide.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055: Port Erreur lors de la lecture du fichier: *.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056: Port Impossible d’ajouter l’entrée, *, au parent.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057: Fusion Impossible d’ajouter des entrées de tableau au dictée.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058: Fusion L’entrée spécifiée doit être un conteneur.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059: Fusion L’entrée, *, contient un index de tableau non valide.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060: Fusion L’entrée, *, n’existe pas.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061: Fusion Erreur lors de la lecture du fichier: *.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062: Définissez : Entrée, *, spécifiée de manière incorrecte.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063: Définissez : L’entrée, *, contient un index de tableau non valide.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064: Définissez : L’entrée, *, n’existe pas.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065: Action de l’éditeur PropertyList inconnue: *.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066: Erreur lors du chargement de' * ': *.

*Tâche MSBuild: PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067: Erreur lors de l’enregistrement de' * ': *.

*Tâche MSBuild: PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx: Messages d’erreur d’exécution

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001: Incompatibilité de version entre le runtime Xamarin. iOS natif et MonoTouch. dll. Réinstallez Xamarin. iOS.

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002: Impossible de trouver la méthode'\*'dans le type'\*'.

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003: Impossible de trouver la méthode générique fermée «\*» sur le type «\*».

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004: Impossible de créer une instance de * pour l’objet natif 0x * (de type' * '), car une autre instance existe déjà pour cet objet natif (de type *).

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005: Le type de\*Wrapper «» ne possède pas sa classe\*ObjectiveC native «».

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006: Impossible de trouver le sélecteur'\*'sur le type'\*'

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007: Impossible d’obtenir le descripteur de méthode\*pour le sélecteur' '\*sur le type' ', car le sélecteur ne correspond pas à une méthode

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008: La version chargée de Xamarin. iOS. dll a été compilée pour * bits, tandis que le processus est * bits. Veuillez signaler un bogue http://bugzilla.xamarin.com dans.

Cela indique qu’il s’agit d’un problème dans le processus de génération. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009: Impossible de trouver la méthode de conversion de bloc à déléguer pour la méthode *.* paramètre s # *. Veuillez signaler un bogue http://bugzilla.xamarin.com dans.

Cela indique qu’une API n’a pas été liée correctement. S’il s’agit d’une API exposée par Xamarin, veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new). S’il s’agit d’une liaison tierce, contactez le fournisseur.

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010: Incompatibilité de taille de type natif entre Xamarin. [iOS | Mac]. dll et l’architecture en cours d’exécution. Xamarin. [iOS | Mac]. dll a été généré pour *-bit, tandis que le processus actuel est *-bit.

Cela indique qu’il s’agit d’un problème dans le processus de génération. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011: Impossible de localiser l’attribut de conversion du délégué pour bloquer ([DelegateProxy]) pour la valeur de retour de la méthode *.* . Veuillez signaler un bogue http://bugzilla.xamarin.com dans.

Xamarin. iOS n’a pas pu localiser une méthode nécessaire au moment de l’exécution (pour convertir un délégué en bloc).

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012: DelegateProxyAttribute non valide pour la valeur de retour de la méthode *.* : DelegateType a la valeur null. Veuillez signaler un bogue http://bugzilla.xamarin.com dans.

L’attribut DelegateProxy de la méthode en question n’est pas valide.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013: DelegateProxyAttribute non valide pour la valeur de retour de la méthode *.* : DelegateType ({2}) spécifie un type sans champ’Handler'. Veuillez signaler un bogue http://bugzilla.xamarin.com dans.

L' `[DelegateProxy]` attribut de la méthode en question n’est pas valide.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014: DelegateProxyAttribute non valide pour la valeur de retour de la méthode *.* : Le champ «Handler{2}» du delegateType () a la valeur null. Veuillez signaler un bogue http://bugzilla.xamarin.com dans.

L' `[DelegateProxy]` attribut de la méthode en question n’est pas valide.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015: DelegateProxyAttribute non valide pour la valeur de retour de la méthode *.* : Le champ delegateType ({2}) 'Handler’n’est pas un délégué, il s’agit d’un *. Veuillez signaler un bogue http://bugzilla.xamarin.com dans.

L’attribut DelegateProxy de la méthode en question n’est pas valide.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016: Impossible de convertir le délégué en bloc pour la valeur de retour de la méthode *.* , parce que l’entrée n’est pas un délégué, il s’agit d’un *. Veuillez signaler un bogue http://bugzilla.xamarin.com dans.

L' `[DelegateProxy]` attribut de la méthode en question n’est pas valide.

Cela indique généralement un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018: Erreur de cohérence interne. Veuillez renseigner un rapport de http://bugzilla.xamarin.com bogue à l’adresse.

Cela indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019: Assembly * introuvable dans les assemblys chargés.

Cela indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020: Impossible de trouver le module avec MetadataToken * dans l’assembly *.

Cela indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021: Type de jeton implicite inconnu: *.

Cela indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022: La référence de jeton * doit être un *, mais il s’agit d’un *. Veuillez renseigner un rapport de http://bugzilla.xamarin.com bogue à l’adresse.

Cela indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023: Un objet d’instance est requis pour construire une méthode générique fermée pour la méthode générique ouverte: * (référence de jeton: *). Veuillez renseigner un rapport de http://bugzilla.xamarin.com bogue à l’adresse.

Cela indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smart_type-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024: Impossible de trouver un type d’extension valide pour l’enum intelligent «{smart_type}». Veuillez signaler un bogue https://bugzilla.xamarin.com dans.

Cela indique un bogue dans Xamarin. iOS. Veuillez envoyer un nouveau problème sur [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).
