---
title: Messages d’erreur Xamarin. Mac (MMP)
description: Ce document répertorie les erreurs générées par MMP, l’outil utilisé pour empaqueter des assemblys compilés dans une application Mac exécutable.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5B26339F-A202-4E41-9229-D0BC9E77868E
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: 35de496d0684473fe3f6bc4ebadddc471b6b0cfe
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292890"
---
# <a name="xamarinmac-error-messages-mmp"></a>Messages d’erreur Xamarin. Mac (MMP)

## <a name="mm0xxx-mmp-error-messages"></a>MM0xxx : messages d’erreur MMP

Par exemple, paramètres, environnement, outils manquants.

<a name="MM0000" />

#### <a name="mm0000-unexpected-error---please-file-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MM0000: Erreur inattendue-Veuillez signaler un rapport de bogue à https://github.com/xamarin/xamarin-macios/issues/new

Une condition d’erreur inattendue s’est produite. Veuillez indiquer le plus d’informations possible dans [un rapport de bogues](https://github.com/xamarin/xamarin-macios/issues/new) , notamment :

* Journaux de génération complets, avec un niveau de détail maximal `-v -v -v -v` (par exemple, dans les **arguments MMP supplémentaires**);
* Un cas de test minimal qui reproduit l’erreur ; les
* Toutes les informations sur les versions

Pour obtenir des informations de version exactes, la méthode la plus simple consiste à utiliser le menu **Xamarin Studio** , **à propos de Xamarin Studio** élément, afficher le bouton **Détails** et copier/coller la version informations (vous pouvez utiliser le bouton **copier les informations** ).

<a name="MM0001" />

#### <a name="mm0001-this-version-of-xamarinmac-requires-mono-0-the-current-mono-version-is-1-please-update-the-monoframework-from-httpmono-projectcomdownloads"></a>MM0001: Cette version de Xamarin. Mac requiert mono {0} (la version mono actuelle est {1}). Veuillez mettre à jour mono. Framework à partir de http://mono-project.com/Downloads

<a name="MM0003" />

#### <a name="mm0003-application-name-0exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MM0003: Le nom d'{0}application'. exe’est en conflit avec un kit de développement logiciel (SDK) ou un nom d’assembly de produit (. dll).

<a name="MM0007" />

#### <a name="mm0007-the-root-assembly-0-does-not-exist"></a>MM0007: L’assembly racine{0}' 'n’existe pas

<a name="MM0008" />

#### <a name="mm0008-you-should-provide-one-root-assembly-only-found-0-assemblies-1"></a>MM0008: Vous devez fournir un assembly racine uniquement, {0} les assemblys trouvés{1}: ' '

<a name="MM0009" />

#### <a name="mm0009-error-while-loading-assemblies-"></a>MM0009: Erreur lors du chargement des assemblys : *.

Une erreur s’est produite lors du chargement des assemblys à partir des références d’assembly racine. Des informations supplémentaires peuvent être fournies dans la sortie de la génération.

<a name="MM0010" />

#### <a name="mm0010-could-not-parse-the-command-line-arguments-0"></a>MM0010: Impossible d’analyser les arguments de ligne de commande :{0}

<!-- 0013 is unused -->

<a name="MM0016" />

#### <a name="mm0016-the-option-0-has-been-deprecated"></a>MM0016: L’option'{0}'est dépréciée.

<a name="MM0017" />

#### <a name="mm0017-you-should-provide-a-root-assembly"></a>MM0017: Vous devez fournir un assembly racine

<a name="MM0018" />

#### <a name="mm0018-unknown-command-line-argument-0"></a>MM0018: Argument de ligne de commande inconnu{0}: ' '

<a name="MM0020" />

#### <a name="mm0020-the-valid-options-for-0-are-1"></a>MM0020: Les options valides pour{0}« » sont{1}« ».

<a name="MM0023" />

#### <a name="mm0023-application-name-0exe-conflicts-with-another-user-assembly"></a>MM0023: Le nom d'{0}application'. exe’est en conflit avec un autre assembly utilisateur.

<a name="MM0026" />

#### <a name="mm0026-could-not-parse-the-command-line-argument-0-1"></a>MM0026: Impossible d’analyser l’argument de ligne de{0}commande' ' :{1}

<a name="MM0043" />

#### <a name="mm0043-the-boehm-garbage-collector-is-not-supported-the-sgen-garbage-collector-has-been-selected-instead"></a>MM0043: Le garbage collector Boehm n’est pas pris en charge. Le garbage collector SGen a été sélectionné à la place.

<a name="MM0050" />

#### <a name="mm0050-you-cannot-provide-a-root-assembly-if---no-root-assembly-is-passed"></a>MM0050: Vous ne pouvez pas fournir un assembly racine si--no-root-assembly est passé.

<a name="MM0051" />

#### <a name="mm0051-an-output-directory---output-is-required-if---no-root-assembly-is-passed"></a>MM0051: Un répertoire de sortie (--output) est requis si--no-root-assembly est passé.

<a name="MM0053" />

#### <a name="mm0053-cannot-disable-new-refcount-with-the-unified-api"></a>MM0053: Impossible de désactiver le nouveau refcount avec l’API unifiée.

<a name="MM0056" />

#### <a name="mm0056-cannot-find-xcode-in-any-of-our-default-locations-please-install-xcode-or-pass-a-custom-path-using---sdkrootpath"></a>MM0056: Xcode introuvable dans l’un de nos emplacements par défaut. Veuillez installer Xcode ou passer un chemin d’accès personnalisé à l’aide de\<--SDKRoot = Path >

<a name="MM0059" />

#### <a name="mm0059-could-not-find-the-currently-selected-xcode-on-the-system-0"></a>MM0059: Impossible de trouver le Xcode actuellement sélectionné sur le système : {0};

<a name="MM0060" />

#### <a name="mm0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned-0-but-that-directory-does-not-exist"></a>MM0060: Impossible de trouver le Xcode actuellement sélectionné sur le système. « Xcode-Select--Print-Path » a retourné{0}«», mais ce répertoire n’existe pas.

<a name="MM0068" />

#### <a name="mm0068-invalid-value-for-target-framework-0"></a>MM0068: Valeur non valide pour la version {0}cible de .NET Framework :.

<a name="MM0071" />

#### <a name="mm0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinmac-please-file-a-bug-report-at-httpsbugzillaxamarincom-with-a-test-case"></a>MM0071: Plateforme inconnue : *. Cela indique généralement un bogue dans Xamarin. Mac ; Veuillez signaler un rapport de bogue dans https://bugzilla.xamarin.com à l’aide d’un cas de test.

Cela indique généralement un bogue dans Xamarin. Mac ; Veuillez signaler un rapport de bogue dans [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac) à l’aide d’un cas de test.

<a name="MM0073" />

#### <a name="mm0073-xamarinmac--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MM0073: Xamarin. Mac * ne prend pas en charge une cible de déploiement de * (la valeur minimale est *). Sélectionnez une cible de déploiement plus récente dans le fichier info. plist de votre projet.

La cible de déploiement minimale est celle spécifiée dans le message d’erreur. Sélectionnez une cible de déploiement plus récente dans le fichier info. plist du projet.

Si la mise à jour de la cible de déploiement n’est pas possible, utilisez une version antérieure de Xamarin. Mac.

<a name="MM0074" />

#### <a name="mm0074-xamarinmac--does-not-support-a-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinmac"></a>MM0074: Xamarin. Mac * ne prend pas en charge une cible de déploiement de * (la valeur maximale est *). Sélectionnez une cible de déploiement plus ancienne dans le fichier info. plist de votre projet, ou effectuez une mise à niveau vers une version plus récente de Xamarin. Mac.

Xamarin. Mac ne prend pas en charge la définition de la cible de déploiement minimale sur une version supérieure à la version pour laquelle cette version particulière de Xamarin. Mac a été créée.

Sélectionnez une cible de déploiement minimale antérieure dans le fichier info. plist du projet ou effectuez une mise à niveau vers une version plus récente de Xamarin. Mac.

<a name="MM0079" />

#### <a name="mm0079-internal-error---no-executable-was-copied-into-the-app-bundle-please-contact-supportxamarincom"></a>MM0079: Erreur interne-aucun fichier exécutable n’a été copié dans l’offre groupée d’applications. Veuillez contacter «support@xamarin.com»

<a name="MM0080" />

#### <a name="mm0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MM0080: La désactivation de NewRefCount,--New-refcount : false, est déconseillée.

<!-- 0088 used by mtouch -->
<!-- 0089 used by mtouch -->

<a name="MM0091" />

#### <a name="mm0091-this-version-of-xamarinmac-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-use-the-dynamic-registrar-or-set-the-managed-linker-behaviour-to-link-platform-or-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MM0091: Cette version de Xamarin. Mac requiert le kit de développement logiciel (SDK) * (fourni avec Xcode *). Mettez à niveau Xcode pour récupérer les fichiers d’en-tête requis ou utilisez le registraire dynamique ou définissez le comportement de l’éditeur de liens managé sur Link Platform ou Link Framework SDK only (pour essayer d’éviter les nouvelles API).

Xamarin. Mac requiert les fichiers d’en-tête, à partir de la version du kit de développement logiciel (SDK) spécifiée dans le message d’erreur, pour générer votre application avec le Bureau d’enregistrement statique. La méthode recommandée pour corriger cette erreur consiste à mettre à niveau Xcode afin d’extraire le kit de développement logiciel (SDK) requis. cela inclut tous les fichiers d’en-tête requis. Si vous avez installé plusieurs versions de Xcode ou si vous souhaitez utiliser un Xcode à un emplacement autre que celui par défaut, veillez à définir l’emplacement de Xcode approprié dans les préférences de votre IDE.

Une autre solution potentielle consiste à activer l’éditeur de liens managé. Cette opération supprime l’API inutilisée, y compris, dans la plupart des cas, la nouvelle API où les fichiers d’en-tête sont manquants (ou incomplets). Toutefois, cela ne fonctionnera pas si votre projet utilise une API qui a été introduite dans un SDK plus récent que celui fourni par Xcode.

Une deuxième solution potentielle, alternative, utilise le Bureau dynamique à la place. Cela entraîne un coût de démarrage en inscrivant dynamiquement les types, mais en supprimant la spécification du fichier d’en-tête. 

Une dernière solution consiste à utiliser une version antérieure de Xamarin. Mac, qui prend en charge le kit de développement logiciel (SDK) dont votre projet a besoin.

<a name="MM0097" />

#### <a name="mm0097-machineconfig-file-0-can-not-be-found"></a>MM0097 : le fichier machine. config{0}«» est introuvable.

<a name="MM0098" />

#### <a name="mm0098-aot-compilation-is-only-available-on-unified"></a>MM0098: La compilation AOA est uniquement disponible sur Unified

<a name="MM0099" />

#### <a name="mm0099-internal-error-0-please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MM0099: Erreur {0}interne. Veuillez signaler un rapport de bogues avec un cas http://bugzilla.xamarin.com) de test (.

<a name="MM0114" />

#### <a name="mm0114-hybrid-aot-compilation-requires-all-assemblies-to-be-aot-compiled"></a>MM0114: La compilation AOA hybride nécessite que tous les assemblys soient compilés par l’AOA.

<a name="MM0129" />

#### <a name="mm0129-debugging-symbol-file-for--does-not-match-the-assembly-and-is-ignored"></a>MM0129: Le fichier de symboles de débogage pour' * 'ne correspond pas à l’assembly et est ignoré.

Les symboles de débogage (fichier PDB portable uniquement) ou. mdb ne peuvent pas être chargés pour l’assembly spécifié.

Cela signifie généralement que l’assembly est plus récent ou plus ancien que les symboles. Dans la mesure où elles ne correspondent pas, elles ne peuvent pas être utilisées et les symboles sont ignorés.

Cet avertissement n’affecte pas l’application en cours de génération. Toutefois, vous ne pourrez peut-être pas le déboguer entièrement (en particulier le code de l’assembly spécifié). Il se peut également que des informations soient manquantes pour les exceptions, les traces de la pile et les rapports d’incidents.

Signalez ce problème à l’éditeur du package d’assembly (par exemple, l’auteur de NuGet) pour que cela puisse être résolu dans les versions ultérieures.

<a name="MM0130" />

#### <a name="mm0130-no-root-assemblies-found-you-should-provide-at-least-one-root-assembly"></a>MM0130: Aucun assembly racine n’a été trouvé. Vous devez fournir au moins un assembly racine.

Lors de `--runregistrar`l’exécution, au moins un assembly racine doit être fourni.

<a name="MM0131" />

#### <a name="mm0131-product-assembly-0-not-found-in-assembly-list-1"></a>MM0131: L’assembly{0}de produit « » est introuvable{1}dans la liste des assemblys : « »

Lors de `--runregistrar`son exécution, la liste des assemblys doit inclure l’assembly Product, Xamarin. Mac, XamMac.

<a name="MM0132" />

#### <a name="mm0132-unknown-optimization--valid-values-are-"></a>MM0132: Optimisation inconnue : *. Les valeurs valides sont : *

L’optimisation spécifiée n’a pas été reconnue.

Le format accepté est `[+|-]optimization-name`, où `optimization-name` est l’une des valeurs indiquées dans le message d’erreur.

Consultez [optimisations de build](https://developer.xamarin.com/guides/cross-platform/macios/build-optimizations) pour obtenir une description complète de chaque optimisation.

<a name="MM0133" />

#### <a name="mm0133-found-more-than-1-assembly-matching-0-choosing-first-1"></a>MM0133: Plus de 1 assembly correspondant à{0}' 'a été trouvé{1}en premier : ' '

<a name="MM0134" />

#### <a name="mm0134-32-bit-applications-should-be-migrated-to-64-bit"></a>MM0134: les applications 32 bits doivent être migrées vers 64 bits.

Apple a annoncé qu’elle n’autorisera pas les envois de l’App Store macOS des applications 32 bits (à partir du 2018 du 1er janvier). 

En outre, les applications 32 bits ne s’exécutent pas sur la version de macOS après High Sierra « sans compromettre ». 

Pour plus d’informations : https://developer.apple.com/news/?id=06282017a

Envisagez de mettre à jour votre application et toutes les dépendances sur 64 bits.

<a name="MM0135" />

#### <a name="mm0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MM0135: L’infrastructure{0}système' 'n’a pas été liée (référencée par l’assembly'{1}') {2} , car elle a été introduite dans {3}et nous utilisons le {2} {4} Kit de développement logiciel (SDK).

Pour générer votre application, Xamarin. Mac doit être lié à des bibliothèques système, dont certaines dépendent de la version du kit de développement logiciel (SDK) spécifiée dans le message d’erreur. Étant donné que vous utilisez une version antérieure du kit de développement logiciel (SDK), les appels à ces API peuvent échouer au moment de l’exécution.

La méthode recommandée pour corriger cette erreur consiste à mettre à niveau Xcode pour récupérer le kit de développement logiciel (SDK) nécessaire. Si plusieurs versions de Xcode sont installées ou si vous souhaitez utiliser un Xcode à un emplacement autre que celui par défaut, veillez à définir l’emplacement Xcode correct dans les préférences de votre IDE.

Vous pouvez également activer l' [éditeur de liens](https://docs.microsoft.com/xamarin/mac/deploy-test/linker) managé pour supprimer les API inutilisées, y compris (dans la plupart des cas) celles qui nécessitent la bibliothèque spécifiée. Toutefois, cela ne fonctionnera pas si votre projet nécessite des API introduites dans un SDK plus récent que celui fourni par Xcode.

En tant que solution de dernière paille, utilisez une version antérieure de Xamarin. Mac qui ne requiert pas que ces nouveaux kits de développement logiciel (SDK) soient présents pendant le processus de génération.

## <a name="mm1xxx-file-copy--symlinks-project-related"></a>MM1xxx : copie de fichier/liens symboliques (lié au projet)

<a name="MM1034" />

#### <a name="mm1034-could-not-create-symlink-file---target-error-number"></a>MM1034: Impossible de créer le lien symbolique' {file} '-> ' {target} ' : erreur {Number}

### <a name="mm14xx-product-assemblies"></a>MM14xx: Assemblages de produits

<a name="MM1401" />

#### <a name="mm1401-the-required-0-assembly-is-missing-from-the-references"></a>MM1401: L’assembly{0}requis «» est manquant dans les références

<a name="MM1402" />

#### <a name="mm1402-the-assembly-0-is-not-compatible-with-this-tool"></a>MM1402: L’assembly{0}' 'n’est pas compatible avec cet outil

<a name="MM1403" />

#### <a name="mm1403-0-1-could-not-be-found-target-framework-0-is-unusable-to-package-the-application"></a>MM1403 : {0} «{1}» est introuvable. Le Framework cible{0}«» est inutilisable pour empaqueter l’application.

<a name="MM1404" />

#### <a name="mm1404-target-framework-0-is-invalid"></a>MM1404: La version cible{0}de .NET Framework n’est pas valide.

<a name="MM1405" />

#### <a name="mm1405-usefullxammacframework-must-always-target-framework-net-45-not-0-which-is-invalid"></a>MM1405 : useFullXamMacFramework doit toujours cibler Framework .net 4,5, et non{0}' ', ce qui n’est pas valide

<a name="MM1406" />

#### <a name="mm1406-target-framework-0-is-invalid-when-targetting-xamarinmac-45-net-framwork"></a>MM1406: La version cible{0}de .NET Framework n’est pas valide lors du ciblage de Xamarin. Mac 4,5 .net Framwork.

<a name="MM1407" />

#### <a name="mm1407-mismatch-between-xamarinmac-reference-0-and-target-framework-selected-1"></a>MM1407: Incompatibilité entre la référence Xamarin.{0}Mac' 'et la version{1}cible de .NET Framework sélectionnée' '.

### <a name="mm15xx-assembly-gathering-not-requiring-linker-errors"></a>MM15xx: Erreurs de collecte d’assembly (ne nécessitant pas l’éditeur de liens)

<a name="MM1501" />

#### <a name="mm1501-can-not-resolve-reference-0"></a>MM1501: Impossible de résoudre la référence :{0}

### <a name="machocs"></a>MachO.cs

<a name="MM1600" />

#### <a name="mm1600-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1600: N’est pas une bibliothèque dynamique Mach-O (en-{0}tête inconnu « {1}0x ») :.

<a name="MM1601" />

#### <a name="mm1601-not-a-static-library-unknown-header-0-1"></a>MM1601: N’est pas une bibliothèque statique (en{0}-tête inconnu {1}«») :.

<a name="MM1602" />

#### <a name="mm1602-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1602: N’est pas une bibliothèque dynamique Mach-O (en-{0}tête inconnu « {1}0x ») :.

<a name="MM1603" />

#### <a name="mm1603-unknown-format-for-fat-entry-at-position-0-in-1"></a>MM1603: Format inconnu pour l’entrée FAT à {0} la {1}position dans.

<a name="MM1604" />

#### <a name="mm1604-file-of-type-0-is-not-a-macho-file-1"></a>MM1604: Le type {0} de fichier n’est pas un fichier{1}MachO ().

## <a name="mm2xxx-linker"></a>MM2xxx: Éditeur de liens

### <a name="mm20xx-linker-general-errors"></a>MM20xx: Erreurs de l’éditeur de liens (général)

<a name="MM2001" />

#### <a name="mm2001-could-not-link-assemblies"></a>MM2001: Impossible de lier les assemblys

<a name="MM2002" />

#### <a name="mm2002-can-not-resolve-reference-0"></a>MM2002: Impossible de résoudre la référence :{0}

<a name="MM2003" />

#### <a name="mm2003-option-0-will-be-ignored-since-linking-is-disabled"></a>MM2003: L’option{0}' 'sera ignorée, car la liaison est désactivée

<a name="MM2004" />

#### <a name="mm2004-extra-linker-definitions-file-0-could-not-be-located"></a>MM2004: Le fichier de définitions de l'{0}éditeur de liens supplémentaire «» est introuvable.

<a name="MM2005" />

#### <a name="mm2005-definitions-from-0-could-not-be-parsed"></a>MM2005: Les définitions de{0}«» n’ont pas pu être analysées.

<a name="MM2006" />

#### <a name="mm2006-native-library-0-was-referenced-but-could-not-be-found"></a>MM2006: La bibliothèque native{0}«» a été référencée mais est introuvable.

<a name="MM2007" />

#### <a name="mm2007-xamarinmac-unified-api-against-a-full-net-profile-does-not-support-linking-pass-the--nolink-flag"></a>MM2007: Xamarin. Mac API unifiée par rapport à un profil .NET complet ne prend pas en charge la liaison. Transmettez l’indicateur-nolink.

<a name="MM2009" />

#### <a name="mm2009-referenced-by-01------this-message-is-related-to-mm2006-"></a>MM2009: Référencé par {0}.{1}     * * Ce message est lié à MM2006 * *

<a name="MM2010" />

#### <a name="mm2010-unknown-httpmessagehandler-0-valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MM2010: HttpMessageHandler `{0}`inconnu. Les valeurs valides sont HttpClientHandler (par défaut), CFNetworkHandler ou NSUrlSessionHandler

<a name="MM2011" />

#### <a name="mm2011-unknown-tlsprovider-0--valid-values-are-default-or-appletls"></a>MM2011: TLSProvider inconnu»{0}.  Les valeurs valides sont default ou appletls

<a name="MM2012" />

#### <a name="mm2012-only-first-0-of-1-referenced-by-warnings-shown--this-message-related-to-2009-"></a>MM2012: Uniquement le {0} premier {1} des avertissements « référencés par » affichés. ** Ce message lié à 2009 \*\*

<a name="MM2013" />

#### <a name="mm2013-failed-to-resolve-the-reference-to-0-referenced-in-1-the-app-will-not-include-the-referenced-assembly-and-may-fail-at-runtime"></a>MM2013: Impossible de résoudre la référence à «{0}», référencée dans «{1}». L’application n’inclut pas l’assembly référencé et peut échouer au moment de l’exécution.

<a name="MM2014" />

#### <a name="mm2014-xamarinmac-extensions-do-not-support-linking-request-for-linking-will-be-ignored--this-message-is-obsolete-in-xm-36-"></a>MM2014: Les extensions Xamarin. Mac ne prennent pas en charge la liaison. La demande de liaison sera ignorée. ** Ce message est obsolète dans XM 3.6 et + \*\*

<!-- 2015 used by mtouch -->

<a name="MM2016" />

#### <a name="mm2016-invalid-tlsprovider-0-option-the-only-valid-value-1-will-be-used"></a>MM2016: Option TlsProvider `{0}` non valide. La seule valeur `{1}` valide sera utilisée.

<a name="MM2017" />

#### <a name="mm2017-could-not-process-xml-description-0"></a>MM2017: Impossible de traiter la description XML :{0}

<a name="MM202x" />

#### <a name="mm202x-binding-optimizer-failed-processing-"></a>MM202x: Le traitement `...`de l’optimiseur de liaison a échoué.

<a name="MM2100" />

#### <a name="mm2100-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2100: Xamarin. Mac API classique ne prend pas en charge la liaison de plateforme.

<a name="MM2103" />

#### <a name="mm2103-error-processing-assembly--"></a>MM2103: Erreur lors du traitement\*de l’assembly' ' : *

Une erreur inattendue s’est produite lors du traitement d’un assembly.

L’assembly à l’origine du problème est nommé dans le message d’erreur. Pour résoudre ce problème, l’assembly doit être fourni dans un rapport de [bogue](https://bugzilla.xamarin.com) avec un journal de génération complet avec commentaires activés ( `-v -v -v -v` par exemple, dans les **arguments mTouch supplémentaires**).

<a name="MM2104" />

#### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: Impossible de lier l’assembly'{0}', car il est en mode mixte.

Les assemblys en mode mixte ne peuvent pas être traités par l’éditeur de liens.

Pour https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies plus d’informations sur les assemblys en mode mixte, consultez.

<a name="MM2106" />

#### <a name="mm2106-could-not-optimize-the-call-to-blockliteralsetupblockunsafe-in--at-offset--because-"></a>MM2106: Impossible d’optimiser l’appel à BlockLiteral. SetupBlock [unsafe] dans * au décalage *, car *.

L’éditeur de liens signale cet avertissement lorsqu’il ne peut pas `BlockLiteral.SetupBlock` optimiser `Block.SetupBlockUnsafe`un appel à ou.

Le message pointera vers la méthode qui appelle `BlockLiteral.SetupBlock[Unsafe]`et peut également fournir des indices sur la raison pour laquelle l’appel n’a pas pu être optimisé.

Signalez un [problème](https://github.com/xamarin/xamarin-macios/issues/new) avec un journal de génération complet afin que nous puissions examiner ce qui s’est produit et éventuellement activer d’autres scénarios à l’avenir.

<a name="MM2107" />

#### <a name="mm2107-its-not-safe-to-remove-the-dynamic-registrar-because-reasons"></a>MM2107: Il n’est pas possible de supprimer le Bureau d’enregistrement dynamique, car {reasons}

L’éditeur de liens signale cet avertissement lorsque le développeur demande la suppression du Bureau d’enregistrement `--optimize:remove-dynamic-registrar` dynamique (en passant à MMP), mais l’éditeur de liens détermine qu’il n’est pas sûr de le faire.

Pour supprimer l’avertissement, supprimez l’argument d’optimisation de MMP ou `--nowarn:2107` passez-le pour l’ignorer.

Par défaut, cette option est activée automatiquement chaque fois que cela est possible et sécurisé.

<a name="MM2108" />

#### <a name="mm2108-0-was-stripped-of-architectures-except-1-to-comply-with-app-store-restrictions-this-could-break-exisiting-codesigning-signatures-consider-stripping-the-library-with-lipo-or-disabling-with---optimize-trim-architectures"></a>MM2108 : «{0}» a été supprimé des architectures, à{1}l’exception de «», pour respecter les restrictions de l’App Store. Cela peut rompre les signatures de coconception existantes. Envisagez de supprimer la bibliothèque avec LiPo ou de désactiver avec--Optimize =-Definition-architectures ");

L’App Store rejette désormais les applications qui contiennent des bibliothèques et des infrastructures qui contiennent des variantes de 32 bits. La bibliothèque a été supprimée des architectures inutilisées lors de la copie dans le bundle d’applications final.

Il s’agit d’une sécurité générale qui réduit la taille du bundle d’applications en tant qu’avantage supplémentaire. Toutefois, toute infrastructure groupée qui est signée par du code aura sa signature invalidée (et resignée ultérieurement si l’application est signée).

Envisagez d’utiliser `lipo` pour supprimer définitivement les architectures inutiles de la bibliothèque source. Si l’application n’est pas publiée dans l’App Store, cette suppression peut être désactivée `--optimize=-trim-architectures` en passant en tant qu’arguments MMP supplémentaires.

<a name="MM2109"/>

#### <a name="mm2109-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2109: Xamarin. Mac API classique ne prend pas en charge la liaison de plateforme.

## <a name="mm3xxx-aot"></a>MM3xxx: AOT

### <a name="mm30xx-aot-general-errors"></a>MM30xx: Erreurs AOA (général)

<a name="MM3001" />

#### <a name="mm3001-could-not-aot-the-assembly-0"></a>MM3001: Impossible d’AOA l’assembly{0}' '

<!-- 3002 used by mtouch -->
<!-- 3003 used by mtouch -->
<!-- 3004 used by mtouch -->
<!-- 3005 used by mtouch -->
<!-- 3006 used by mtouch -->
<!-- 3007 used by mtouch -->
<!-- 3008 used by mtouch -->

<a name="MM3009" />

#### <a name="mm3009-aot-of-0-was-requested-but-was-not-found"></a>MM3009: L’AOA de{0}' 'a été demandé, mais est introuvable

<a name="MM3010" />

#### <a name="mm3010-exclusion-of-aot-of-0-was-requested-but-was-not-found"></a>MM3010: L’exclusion de l’AOA{0}de «» a été demandée, mais est introuvable

## <a name="mm4xxx-code-generation"></a>MM4xxx : génération de code

### <a name="mm40xx-driverm"></a>MM40xx : Driver. m

<a name="MM4001" />

#### <a name="mm4001-the-main-template-could-not-be-expanded-to-0"></a>MM4001: Impossible d’étendre le modèle principal à `{0}`.

### <a name="mm41xx-registrar"></a>MM41xx : registraire

<a name="MM4134" />

#### <a name="mm4134-your-application-is-using-the-0-framework-which-isnt-included-in-the-macos-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-osx-2-while-youre-building-with-the-macos-1-sdk-this-configuration-is-not-supported-with-the-static-registrar-pass---registrardynamic-as-an-additional-mmp-argument-in-your-projects-mac-build-option-to-select-alternatively-select-a-newer-sdk-in-your-apps-mac-build-options"></a>MM4134: Votre application utilise l’infrastructure «{0}», qui n’est pas incluse dans le kit de développement logiciel (SDK) MacOS que vous utilisez pour créer votre application {2}(cette infrastructure a été introduite dans {1} OSX, pendant que vous créez avec le kit de développement logiciel (SDK) MacOS). Cette configuration n’est pas prise en charge avec le Bureau d’enregistrement statique (Pass--registraire : Dynamic comme argument MMP supplémentaire dans l’option de build Mac de votre projet à sélectionner). Vous pouvez également sélectionner un nouveau kit de développement logiciel (SDK) dans les options de build Mac de votre application.

<a name="MM4173" />

#### <a name="mm4173-the-registrar-cant-compute-the-block-signature-for-the-delegate-of-type-delegate-type-in-the-method-method-because-"></a>MM4173: Le Bureau d’enregistrement ne peut pas calculer la signature de bloc pour le délégué de type {delegate-type} dans la méthode {Method}, car *.

Il s’agit d’un avertissement indiquant que le Bureau d’enregistrement n’a pas pu injecter la signature de bloc de la méthode spécifiée dans le code d’Registrar généré, car le Bureau d’enregistrement n’a pas pu le calculer.

Cela signifie que la signature de bloc doit être calculée au moment de l’exécution, ce qui est un peu plus lent.

Il existe actuellement deux raisons possibles pour cet avertissement :

1. Le type du délégué managé est `System.Delegate` ou. `System.MulticastDelegate` Ces types ne représentent pas une signature spécifique, ce qui signifie que le Bureau d’enregistrement ne peut pas calculer la signature native correspondante. Dans ce cas, le correctif consiste à utiliser un type délégué spécifique pour le bloc (sinon, l’avertissement peut être ignoré en `--nowarn:4173` ajoutant comme argument MMP supplémentaire dans les options de build Mac du projet).
2. Le Bureau d’enregistrement ne `Invoke` peut pas trouver la méthode du délégué. Cela ne devrait pas se produire. Veuillez donc [Envoyer un problème](https://github.com/xamarin/xamarin-macios/issues/new) avec un projet de test afin de pouvoir le corriger.

<a name="MT4174" />

#### <a name="mt4174-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-methods-parameter-parameter"></a>MT4174: Impossible de trouver la méthode de conversion de bloc à déléguer pour le paramètre # {parameter} de la méthode {Method}.

Il s’agit d’un avertissement indiquant que le Bureau d’enregistrement statique n’a pas pu trouver la méthode permettant de créer un délégué pour un bloc objective-C. Une tentative sera effectuée au moment de l’exécution pour trouver la méthode, mais elle échouera probablement également (avec une exception MT8009).

Cette erreur peut être due à l’écriture manuelle de liaisons pour une API qui utilise des blocs. Il est recommandé d’utiliser un projet de liaison pour lier du code Objective-C, en particulier lorsqu’il implique des blocs. dans la mesure où il est assez compliqué de le faire juste lorsque vous le faites manuellement.

Si ce n’est pas le cas, veuillez [Envoyer un problème](https://github.com/xamarin/xamarin-macios/issues/new) avec un cas de test.

## <a name="mm5xxx-gcc-and-toolchain"></a>MM5xxx: GCC et chaîne d’outils

### <a name="mm51xx-compilation"></a>MM51xx : compilation

<a name="MM5101" />

#### <a name="mm5101-missing-0-compiler-please-install-xcode-command-line-tools-component"></a>MM5101: Compilateur'{0}'manquant. Installez le composant « outils en ligne de commande » Xcode.

<!-- 5102 used by mtouch -->

<a name="MM5103" />

#### <a name="mm5103-failed-to-compile-error-code---0-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM5103: Échec de la compilation. Code d’erreur {0}-. Veuillez signaler un rapport de bogue à http://bugzilla.xamarin.com

<!-- 5104 used by mtouch -->

### <a name="mm52xx-linking"></a>MM52xx : liaison

<a name="MM5202" />

#### <a name="mm5202-monoframework-mdk-is-missing-please-install-the-mdk-for-your-monoframework-version-from-httpmono-projectcomdownloads"></a>MM5202: Mono. Framework MDK est manquant. Installez le MDK pour votre version mono. Framework à partir de http://mono-project.com/Downloads

<a name="MM5203" />

#### <a name="mm5203-cant-find-libxammaca-likely-because-of-a-corrupted-xamarinmac-installation-please-reinstall-xamarinmac"></a>MM5203: Impossible de trouver libxammac. a, probablement en raison d’une installation Xamarin. Mac endommagée. Réinstallez Xamarin. Mac.

<a name="MM5204" />

#### <a name="mm5204-invalid-architecture-x86_64-is-only-supported-on-non-classic-profiles"></a>MM5204: Architecture non valide. x86_64 est pris en charge uniquement sur les profils non classiques.

<a name="MM5205" />

#### <a name="mm5205-invalid-architecture-0-valid-architectures-are-i386-and-x86_64-when---profilemobile"></a>MM5205: Architecture non valide{0}' '. Les architectures valides sont i386 et x86_64 (When--Profile = mobile).

<a name="MM5218" />

#### <a name="mm5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MM5218: Impossible d’ignorer le symbole dynamique {Symbol} (--ignore-Dynamic-Symbol = {Symbol}), car il n’a pas été détecté en tant que symbole dynamique.

Consultez l' [Avertissement mTouch équivalent](~/ios/troubleshooting/mtouch-errors.md#MT5218).

<!-- 5206 used by mtouch -->
<!-- 5207 used by mtouch -->
<!-- 5208 used by mtouch -->
<!-- 5209 used by mtouch -->
<!-- 5210 used by mtouch -->
<!-- 5211 used by mtouch -->
<!-- 5212 used by mtouch -->
<!-- 5213 used by mtouch -->
<!-- 5214 used by mtouch -->
<!-- 5215 used by mtouch -->
<!-- 5216 used by mtouch -->
<!-- 5217 used by mtouch -->

### <a name="mm53xx-other-tools"></a>MM53xx : autres outils

<a name="MM5301" />

#### <a name="mm5301-pkg-config-could-not-be-found-please-install-the-monoframework-from-httpmono-projectcomdownloads"></a>MM5301 : pkg-config est introuvable. Installez le mono. Framework à partir de http://mono-project.com/Downloads

<!-- 5302 used by mtouch -->
<!-- 5303 used by mtouch -->
<!-- 5304 used by mtouch -->

<a name="MM5305" />

#### <a name="mm5305-missing-otool-tool-please-install-xcode-command-line-tools-component"></a>MM5305: Outil’otool’manquant. Installez le composant outils en ligne de commande Xcode

<a name="MM5306" />

#### <a name="mm5306-missing-dependencies-please-install-xcode-command-line-tools-component"></a>MM5306: Dépendances manquantes. Installez le composant outils en ligne de commande Xcode

<a name="MM5308" />

#### <a name="mm5308-xcode-license-agreement-may-not-have-been-accepted--please-launch-xcode"></a>MM5308: Le contrat de licence Xcode n’a peut-être pas été accepté.  Veuillez lancer Xcode.

<a name="MM5309" />

#### <a name="mm5309-native-linking-failed-with-error-code-1-check-build-log-for-details"></a>MM5309: Échec de la liaison native avec le code d’erreur 1. Pour plus d’informations, consultez le journal de génération.

<a name="MM5310" />

#### <a name="mm5310-install_name_tool-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5310 : échec de install_name_tool avec le code d'{0}erreur' '. Pour plus d’informations, consultez le journal de génération.

<a name="MM5311" />

#### <a name="mm5311-lipo-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5311 : échec de LiPo avec le code d'{0}erreur' '. Pour plus d’informations, consultez le journal de génération.

<!-- MM6xxx: mmp internal tools -->
<!-- MM7xxx: reserved -->

## <a name="mm8xxx-runtime"></a>MM8xxx : Runtime

### <a name="mm800x-misc"></a>MM800x : divers

<!-- 8000 used by mtouch -->
<!-- 8001 used by mtouch -->
<!-- 8002 used by mtouch -->
<!-- 8003 used by mtouch -->
<!-- 8004 used by mtouch -->
<!-- 8005 used by mtouch -->
<!-- 8006 used by mtouch -->
<!-- 8007 used by mtouch -->
<!-- 8008 used by mtouch -->
<!-- 8009 used by mtouch -->
<!-- 8010 used by mtouch -->
<!-- 8011 used by mtouch -->
<!-- 8012 used by mtouch -->
<!-- 8013 used by mtouch -->
<!-- 8014 used by mtouch -->
<!-- 8015 used by mtouch -->
<!-- 8016 used by mtouch -->

<a name="MM8017" />

#### <a name="mm8017-the-boehm-garbage-collector-is-not-supported-please-use-sgen-instead"></a>MM8017: Le garbage collector Boehm n’est pas pris en charge. Utilisez SGen à la place.

<a name="MM8025" />

#### <a name="mm8025-failed-to-compute-the-token-reference-for-the-type-typeassemblyqualifiedname-because-reasons"></a>MM8025: Échec du calcul de la référence de jeton pour le type' {type. AssemblyQualifiedName}», car {reasons}

Cela indique un bogue dans Xamarin. Mac. Veuillez signaler un bogue [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac)dans.

Une solution de contournement possible consiste à désactiver `register-protocols` l’optimisation, en `--optimize:-register-protocols` passant en tant qu’argument MMP supplémentaire dans les options de build Mac du projet.

<a name="MM8026" />

#### <a name="mm8026--is-not-supported-when-the-dynamic-registrar-has-been-linked-away"></a>MM8026 : * n’est pas pris en charge lorsque le Bureau d’enregistrement dynamique a été lié.
 
Cela indique généralement un bogue dans Xamarin. Mac, car le Bureau d’enregistrement dynamique ne doit pas être lié s’il est nécessaire. Veuillez signaler un bogue [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)dans.
 
Il est possible de forcer l’éditeur de liens à conserver le Bureau d’enregistrement `--optimize=-remove-dynamic-registrar` dynamique en ajoutant aux arguments MMP supplémentaires dans les options de build Mac du projet.
