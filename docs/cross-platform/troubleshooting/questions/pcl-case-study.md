---
title: Résoudre les problèmes liés au flux de données System. Diagnostics. Tracing et TPL
description: 'Étude de cas de bibliothèque de classes portable : Comment résoudre des problèmes liés à System.Diagnostics.Tracing pour le package NuGet Microsoft TPL Dataflow ?'
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: davidortinau
ms.author: daortin
ms.openlocfilehash: e7c0bcc7450ab718659723293f995c83b4dc517a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013572"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>Étude de cas de bibliothèque de classes portable : Comment résoudre des problèmes liés à System.Diagnostics.Tracing pour le package NuGet Microsoft TPL Dataflow ?

> [!IMPORTANT]
> Cet exemple particulier de `System.Diagnostic.Tracing` ne produit plus d’erreurs par défaut dans les versions les plus récentes de Xamarin. Bien que la solution suggérée fonctionne toujours, Notez que certains des bogues mentionnés dans la section « couches d’erreurs » ont été corrigés.
> En outre, vous devez noter que .NET Standard est désormais la méthode recommandée pour implémenter des API .NET multiplateforme.

## <a name="summary"></a>Récapitulatif

Xamarin. iOS et Xamarin. Android n’implémentent pas 100% de chaque profil PCL qu’ils autorisent en tant que références. Pour des raisons pratiques dans Visual Studio pour Mac, Visual Studio et le gestionnaire de package NuGet, les projets Xamarin autorisent l’utilisation de plusieurs profils qui n’ont que des implémentations _incomplètes_ . Par exemple, ni Xamarin. iOS ni Xamarin. Android n’incluent actuellement une implémentation complète des types dans l’espace de noms PCL « System. Diagnostics. Tracing ». Cette limitation provoque trois couches d’erreurs lors de la tentative d’utilisation de la version par défaut `portable-net45+win8+wpa81` du package NuGet de flux de données Microsoft TPL.

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Solution de contournement : basculer le projet d’application pour référencer la version `portable-net45+win8+wp8+wpa81` de la bibliothèque de flux de données TPL

(Cela évite les trois couches d’erreurs et fonctionne pour toutes les versions récentes de Xamarin.)

1. Ouvrez le fichier projet d’application **. csproj** dans un éditeur de texte.

2. Recherchez la ligne qui ressemble à ceci :

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Remplacez `portable-net45+win8+wpa81` par `portable-net45+win8+wp8+wpa81` (`+wp8` est ajouté) :

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Explication

La version `portable-net45+win8+wp8+wpa81` de la bibliothèque ne fait pas référence _à_ **System. Diagnostics. Tracing. dll** . elle évite donc complètement les trois couches de problèmes.

### <a name="limitations"></a>Limitations

- La version `portable-net45+win8+wp8+wpa81` de la bibliothèque peut ne pas inclure 100% des fonctionnalités de la version de `portable-net45+win8+wpa81`.

- Le gestionnaire de package NuGet installe la version `portable-net45+win8+wpa81` du package NuGet PCL par défaut. vous devez donc ajuster la référence manuellement.

## <a name="details-about-the-three-layers-of-errors"></a>Détails sur les trois couches d’erreurs

1. L’assembly de façade **System. Diagnostics. Tracing. dll** est actuellement absent de toutes les versions Mac de Xamarin. Android (bogue non public 34888) et il n’est pas présent dans toutes les versions de Xamarin. iOS inférieures à 9,0 (ou inférieures à XamarinVS 3.11.1443 sur Windows) (corrigé [Bogue 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Ce problème entraînera l’une des erreurs suivantes, en fonction de la cible de déploiement et des paramètres de l’éditeur de liens :

    - Xamarin. Android. Common. targets : erreur : exception lors du chargement des assemblys : System. IO. FileNotFoundException : impossible de charger l’assembly’System. Diagnostics. Tracing, version = 4.0.0.0, culture = neutral, PublicKeyToken = b03f5f7f11d50a3a'. Peut-être n’existe-t-il pas dans le profil mono pour Android ?

    - Impossible de charger le fichier ou l’assembly’System. Diagnostics. Tracing’ou l’une de ses dépendances. Le système ne trouve pas le fichier spécifié. (System. IO. FileNotFoundException)

    - MTOUCH : Error MT3001 : impossible d’AOA l’assembly'/Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll'

    - MTOUCH : Error MT2002 : échec de la résolution de l’assembly : 'System. Diagnostics. Tracing, version = 4.0.0.0, culture = neutral, PublicKeyToken = b03f5f7f11d50a3a'

2. Certaines surcharges de méthode sont manquantes [dans l’implémentation mono actuelle des types dans « System. Diagnostics. Tracing »](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) ([bogue 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Ce problème entraînera l’une des erreurs suivantes de l’éditeur de liens lors de la création d’une application Xamarin :

    - /Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets : erreur : erreur lors de l’exécution de la tâche LinkAssemblies : erreur XA2006 : référence à l’élément de métadonnées System. void System. Diagnostics. Tracing. EventSource :: WriteEvent (System. Int32, System. Object []) ' (défini dans’System. Threading. Tasks. flux de données, version = 4.5.24.0, culture = neutral, PublicKeyToken = b03f5f7f11d50a3a') à partir de’System. Threading. Tasks. flux de données, version = 4.5.24.0, culture = neutral, PublicKeyToken = b03f5f7f11d50a3a’n’a pas pu être résolu.

    - MTOUCH : Error MT2002 : échec de la résolution de la référence « System. void System. Diagnostics. Tracing. EventSource :: WriteEvent (System. Int32, System. Object []) » à partir de «System. Diagnostics. Tracing, version = 4.0.0.0, culture = neutral, PublicKeyToken = b03f5f7f11d50a3a

3. L' [implémentation mono actuelle des types dans « System. Diagnostics. Tracing »](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) est également une implémentation « factice » _vide_ actuellement ([bogue 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Toute tentative d’utilisation de ces méthodes au moment de l’exécution peut donc produire des résultats inattendus. Pour le cas particulier de la bibliothèque de flux de _données_ Microsoft TPL, il semble que les appels à `WriteEvent(System.Int32,System.Object[])` ne soient pas essentiels pour la majeure partie du comportement de la bibliothèque, donc le correctif pour « couche 2 » ([bogue 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), ajout d’implémentations vides) sera probablement suffisant pour la plupart des cas d’utilisation de flux de données Microsoft TPL.

## <a name="questions--answers"></a>Questions & réponses

### <a name="i-was-able-to-leave-linking-enabled-with-the-portable-net45win8wpa81-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>J’ai réussi à conserver la liaison activée avec la version `portable-net45+win8+wpa81` de la bibliothèque sur les versions antérieures de Xamarin. iOS ou sur Xamarin. Android. Comment cela fonctionne-t-il ?

#### <a name="answer"></a>réponse

Il est _possible_ de faire en sorte que la build se termine correctement (avec la liaison activée) dans les versions antérieures de Xamarin. iOS ou dans Xamarin. Android sur Mac si vous incluez une référence à l' _assembly de référence_ `System.Diagnostics.Tracing.dll` \[1\] plutôt que le l' _assembly de façade_ \[2], mais malheureusement il ne s’agit pas d’une solution de contournement « correcte ». Les assemblys de référence sont uniquement destinés à être utilisés lors de la création de _bibliothèques portables_, et non de code spécifique à la plateforme, comme les applications. Toute tentative d' _exécution_ du code contenu dans des assemblys de référence (plutôt que de simplement le créer) risque de produire des résultats inattendus. Le correctif approprié permettra à l’équipe mono d’ajouter la surcharge de `WriteEvent(System.Int32,System.Object[])` manquant au type de [`EventSource`](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) ([bogue 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Pour le moment, la meilleure option consiste à passer à la version `portable-net45+win8+wp8+wpa81` de la bibliothèque de flux de données Microsoft TPL, comme indiqué dans la section de contournement ci-dessus.

(Pour toute personne qui lit cet article après avoir consulté une réponse plus ancienne et plus rapide de StackOverflow (<https://stackoverflow.com/a/23591322/2561894>), Notez que la distinction entre les assemblys de référence et l’assembly de façade n’a _pas_ été mentionnée ici.)

**emplacements de\[1\] « assembly de référence »**

Windows : `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (mono) : `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**emplacements de\[2\] « assembly facade »**

Windows : `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (mono) : `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>Est-il utile si j’ajoute manuellement une référence à l’assembly de façade « System. Diagnostics. Tracing » ?

_En particulier, puis-je résoudre le problème à l’aide de ces deux étapes ?_

1. _Copiez l’assembly de façade `System.Diagnostics.Tracing.dll` dans le dossier du projet d’application à partir de l’un des emplacements suivants :_

    Windows : `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (mono) : `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _Ajoutez une référence à l’assembly de façade dans le projet d’application Xamarin. iOS ou Xamarin. Android._

#### <a name="answer"></a>réponse

Non, cela n’est pas utile.

- Pour Xamarin. iOS 9,0 ou toute version récente de Xamarin. Android sur Windows, cette solution de contournement est strictement redondante et peut provoquer des erreurs de compilation similaires à « un assembly ». Diagnostics. Tracing « avec la même identité a déjà été importé ».

- Pour Xamarin. iOS 8,10 ou une partie inférieure ou pour Xamarin. Android sur Mac, cette solution de contournement est utile, mais _uniquement_ pour le problème d’assembly « couche 1 » manquant. Cela ne résoudra _pas_ les erreurs de l’éditeur de liens « couche 2 ». il ne s’agit donc pas d’une solution complète.

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>Puis-je utiliser le [package NuGet System. Diagnostics. Tracing](https://www.nuget.org/packages/System.Diagnostics.Tracing/) pour résoudre le problème ?

#### <a name="answer"></a>réponse

Non, le package NuGet 3,0 « System. Diagnostics. Tracing » comprend uniquement des implémentations spécifiques à la plateforme pour « DNXCore50 » et « netcore50 ». Il _omet_ explicitement les implémentations pour Xamarin. Android (« monoandroid ») et Xamarin. iOS (« MonoTouch » et « xamarinios »). Cela signifie que l’installation du package n’aura _aucun effet_ pour les projets Xamarin. Android et Xamarin. iOS. Le package NuGet suppose que ces deux plateformes fournissent leur _propre_ implémentation des types. Cette hypothèse est « correcte » dans le sens où mono a _une_ implémentation de l’espace de noms, mais comme indiqué sous points \#2 et \#3 de « détails sur les trois couches d’erreurs » ci-dessus, l’implémentation est actuellement incomplète. Le correctif approprié permettra donc à l’équipe mono de résoudre le [bogue 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) et le [bogue 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide, pour nous contacter ou, si le problème persiste même après l’utilisation des informations ci-dessus, consultez [les options de support disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, les suggestions et la façon de signaler un nouveau bogue si nécessaire. .
