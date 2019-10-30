---
title: Forum aux questions sur Xamarin. iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e79fca8c59ae49d27cd335106ca57945be106031
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031084"
---
# <a name="ios-frequently-asked-questions"></a>Forum aux questions sur iOS

## <a name="general-questions"></a>Questions générales

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[Puis-je utiliser une machine virtuelle Mac avec Xamarin ?](mac-vm.md)
Oui, mais uniquement sur le matériel Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[Comment passer à une version antérieure de Xcode ?](downgrade-xcode.md)
Ce guide fournit des liens pour accéder aux versions précédentes de Xcode, ainsi qu’à la dernière version.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[Où puis-je définir l’emplacement de mon Kit SDK iOS ?](ios-sdk.md)
Pour la plupart des utilisateurs, ceux-ci sont automatiquement définis sur les emplacements appropriés. Ce guide répertorie les emplacements du kit de développement logiciel (SDK) par défaut et comment les modifier si nécessaire.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[Comment puis-je réactiver les options de développement après la mise à jour d’iOS ?](update-developer-options.md)
Un bogue iOS peut entraîner la disparition des options de développement après la mise à jour des versions d’iOS, ce qui a été observé lors du passage à iOS 8. x. Ce guide décrit comment les options peuvent être réactivées.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[L’emplacement de l’utilisateur ne fonctionne ne pas dans iOS 8](ios8-user-location.md)
Ce guide vous explique comment modifier le fichier info. plist pour activer l’emplacement de l’utilisateur dans iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[Où puis-je trouver le fichier .dSYM pour générer les symboles des rapports d'incidents iOS ?](symbolicate-ios-crash.md)
Ce guide décrit les étapes de base des journaux d’incidents symbolicating iOS pour vous aider à diagnostiquer les incidents. Il contient également des liens vers des ressources supplémentaires pour des techniques de symboles plus avancées & des informations sur l’interprétation des journaux d’incidents iOS.

### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[Comment définir des variables d’environnement Mono Runtime pour des projets iOS dans Xamarin Studio ?](xs-mono-runtime.md)
Si vous devez définir des variables d’environnement d’exécution pour mono, vous pouvez les définir dans les **options de projet > page exécuter > général** .

## <a name="publishing-questions"></a>Questions sur la publication

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Erreur lors de l’envoi à l’App Store : « un bundle non valide-options non autorisées à être incorporées dans bitcode sont détectées dans la soumission »](invalid-bundle-bitcode.md)

L’envoi d’applications _nécessitant_ bitcode, telles que Watchos et les applications tvOS, doit être effectué avec Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[Puis-je modifier le chemin de sortie du fichier de la Loi sur la Loi ?](ipa-output-path.md)
À partir de Xamarin cycle 7, vous pouvez utiliser des cibles MSBuild personnalisées pour y parvenir.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[Comment puis-je copier les fichiers de sortie de la liste de réutilisation dans le dossier de dépôt TFS ?](ipa-tfs.md)
Oui, ce guide explique comment procéder.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[Puis-je ajouter ou supprimer des fichiers dans un fichier de vue de la loi après l’avoir généré dans Visual Studio ?](modify-ipa.md)
Oui, c’est possible, mais il est généralement nécessaire de signer à nouveau le groupe de `.app` après avoir apporté la modification. Notez que la modification du fichier de `.ipa` n’est pas nécessaire dans un usage normal. Cet article est fourni exclusivement à titre d’information.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[Est-il possible de créer une archive. xcarchive à partir de Visual Studio ?](create-xcarchive.md)
À partir de Xamarin 4, il est désormais possible de créer une `.xcarchive` à partir de Windows en affectant à la propriété `ArchiveOnBuild` la valeur `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[Pourquoi ma soumission d’application échoue-t-elle avec le message : « Disallowed paths ( "iTunesMetadata.plist" ) found at ... » ?](itunesmetadata-disallowed-paths.md)
Cette erreur est le résultat d’une modification dans le processus de vérification de l’App Store d’Apple. Cette erreur spécifique n’est _pas_ liée à la version particulière de Xamarin que vous avez installée. par conséquent, la rétrogradation n’est _pas_ utile. Ce guide fournit des liens vers des informations supplémentaires sur la façon de résoudre le problème.

## <a name="diagnosing-specific-error-messages"></a>Diagnostic de messages d’erreur spécifiques

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Erreur de concepteur iOS avec RegisterServicePort](error-registerserviceport.md)
Les erreurs avec des `RegisterServicePort` et des messages d’erreur similaires comme ci-dessus sont généralement un problème avec les logiciels espions/programmes malveillants sur l’ordinateur. Ce guide détaille la confirmation du diagnostic et des informations sur la suppression des logiciels espions/logiciels malveillants.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[Pourquoi ma build iOS échoue-t-elle avec le message : no valid iPhone code signing keys found in keychain ?](no-codesigning-keys.md)
Ce message d’erreur se produit lorsque le projet en question recherche des informations d’identification de signature de code valides, mais ne parvient pas à les trouver. La signature de code est requise pour les tests et les déploiements sur des appareils iOS physiques ; et les builds ad hoc & App Store.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[Pourquoi mon application iOS 9 échoue-t-elle avec le message : System.Exception: Failed to marshal the Objective-C object ?](exception-marshal-obj-c.md)
Dans iOS 9, les modifications d’API requièrent l’utilisation d’un constructeur de rappel lors de l’appel de code non managé, car l’API sous-jacente l’attend à présent.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Erreur d’exécution : l’assembly mscorlib.dll est introuvable ou ne peut pas être chargé](error-mscorlib-not-found.md)
Ce problème se produit lorsque les dossiers *masqués* `.monotouch-32` et `.monotouch-64` sont absents du `.xcarchive` pour la création de la signature/la disponibilité, déclenchant l’erreur d’exécution.

### <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocationerror-encode-offset-scattered-relocationmd"></a>[Erreur de compilation : impossible d’encoder l’offset X dans le réadressage éparpillé résultant](error-encode-offset-scattered-relocation.md)
Ce problème se produit lors de la génération d’architectures 32 bits, telles que ARMv7, lorsque le fichier binaire final est trop grand pour le chaîne d’outils natif.

## <a name="deprecated"></a>Déconseillé

> [!IMPORTANT]
> Les articles ci-dessous s’appliquent aux problèmes qui ont été résolus dans les versions récentes de Xamarin. Toutefois, si le problème se produit sur la version la plus récente du logiciel, envoyez un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) à vos informations de contrôle de version et à la sortie complète du journal de génération.

### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[Le fichier IPA contient 0 octet](ipa-zero-bytes.md)
Certains problèmes connus dans les versions précédentes de Xamarin pouvaient entraîner la présence de 0 octets dans le fichier de la Loi d’accès aux fichiers sur Windows.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[Erreur IBTool : impossible d’effectuer l’opération.](error-ibtool.md)
Apple a [corrigé](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) ce bogue `ibtool` dans Xcode 6.1.1. par conséquent, la mise à niveau vers Xcode 6.1.1 ou une version ultérieure est la solution la plus simple.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[Erreur MT1009 : impossible de copier l’assembly](error-mt1009.md)
Cela affecte les utilisateurs exécutant Xamarin. iOS 7.2.6. Ce problème est dû aux autorisations de fichier nécessitant des privilèges plus élevés quand Xamarin. iOS est installé avec un compte d’utilisateur différent, puis le compte principal du développeur.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[Erreur System.Exception AMDeviceNotificationSubscribe retournée ...](exception-amddevicenotificationsubscribe.md)
Ce message peut s’afficher dans une boîte de dialogue d’erreur lorsque vous démarrez Visual Studio pour Mac pour la première fois, ou dans le fichier `mtbserver.log`. Notez qu’il s’agit d’un problème rare. Si Visual Studio ne parvient pas à se connecter à l’hôte de build Mac, il y a d’autres erreurs qui sont plus susceptibles d’apparaître dans le fichier `mtbserver.log`.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe introuvable found rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Cette erreur peut s’afficher dans le `Mac Server Log` dans Visual Studio.
