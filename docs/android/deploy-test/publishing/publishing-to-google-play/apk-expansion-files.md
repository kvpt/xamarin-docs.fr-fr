---
title: Fichiers d’extension d’APK
ms.prod: xamarin
ms.assetid: DB7E38E8-3C4E-5191-27EA-22DE63044FE2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 712322435614966348fc5c10cabf724870c307e4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021295"
---
# <a name="apk-expansion-files"></a>Fichiers d’extension d’APK

Certaines applications (certains jeux, par exemple) ont besoin de plus de ressources et composants que ce qui peut être proposé dans la limite de taille maximale des applications Android imposée par Google Play. Cette limite dépend de la version Android ciblée par votre APK :

- 100 Mo pour les APK ciblant Android 4.0 ou version supérieure (niveau d’API 14 ou supérieur).
- 50 Mo pour les APK ciblant Android 3.2 ou version inférieure (niveau d’API 13 ou supérieur).

Pour contourner cette limitation, Google Play hébergera et distribuera deux *fichiers d’extension* avec un APK, ce qui permet à une application de dépasser indirectement cette limite. 

Sur la plupart des appareils, quand une application est installée, les fichiers d’extension sont téléchargés en même temps que l’APK et sont enregistrés à l’emplacement de stockage partagé (la carte SD ou la partition montable en USB) sur l’appareil. Sur certains appareils plus anciens, les fichiers d’extension ne sont pas installés automatiquement avec l’APK. Dans ce cas, l’application doit contenir du code qui télécharge les fichiers d’extension lorsque l’utilisateur exécute l’application pour la première fois.

Les fichiers d’extension sont traités comme des fichiers *obb (opaque binary blobs)* et leur taille est limitée à 2 Go. Android n’effectue aucun traitement particulier sur ces fichiers après leur téléchargement&ndash; Ils peuvent utiliser tout format approprié pour l’application. Sur le plan conceptuel, l’approche recommandée en matière de fichiers d’extension est la suivante :

- **Extension principale **  &ndash; ce fichier est le fichier d’extension principal pour les ressources et les composants qui dépassent la limite de taille de l’APK. Le fichier d’extension principal doit contenir les principaux composants dont une application a besoin et doit rarement être mis à jour.
- **Extension corrective **  &ndash; ce fichier est conçu pour les petites mises à jour du fichier d’extension principal. Ce fichier peut être mis à jour. L’application est responsable d’effectuer les correctifs ou mises à jour nécessaires à partir de ce fichier.

Les fichiers d’extension doivent être chargés en même temps que l’APK.
Google Play n’autorise pas le chargement d’un fichier d’extension sur un APK existant ou pour des APK existants à mettre à jour. Si un fichier d’extension doit être mis à jour, un nouvel APK doit être chargé avec le `versionCode` mis à jour.

## <a name="expansion-file-storage"></a>Stockage des fichiers d’extension

Lorsque les fichiers sont téléchargés sur un appareil, ils sont stockés à l’emplacement **_shared-store_/Android/obb/_package-name_** :

- **_shared-store_** &ndash; Il s’agit du répertoire spécifié par `Android.OS.Environment.ExternalStorageDirectory` .
- **_package-name_** &ndash; Il s’agit du nom de paquet Java de l’application.

Une fois téléchargés, les fichiers d’extension ne doivent pas être déplacés, modifiés, renommés ou supprimés de leur emplacement sur l’appareil. S’ils le sont, les fichiers d’extension sont à nouveau téléchargés et les anciens fichiers sont supprimés. En outre, le répertoire des fichiers d’extension ne doit contenir que les fichiers du pack d’extension.

Les fichiers d’extension n’offrent aucune sécurité ni protection de leur contenu&ndash; D’autres applications ou utilisateurs peuvent accéder à tous les fichiers enregistrés sur le stockage partagé.

Si un fichier d’extension doit être décompressé, les fichiers décompressés doivent être stockés dans un répertoire différent, par exemple dans `Android.OS.Environment.ExternalStorageDirectory`.

Plutôt que d’extraire les fichiers d’un fichier d’extension, vous avez également la possibilité de lire les composants ou les ressources directement à partir du fichier d’extension. Un fichier d’extension n’est rien de plus qu’un fichier zip qui peut être utilisé avec un `ContentProvider` approprié. [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) contient un assembly, [System.IO.Compression.Zip](https://github.com/mattleibow/Android.Play.ExpansionLibrary/tree/master/System.IO.Compression.Zip), qui inclut un `ContentProvider` qui autorise l’accès direct aux fichiers pour certains fichiers multimédias. Si les fichiers multimédias sont regroupés dans un fichier zip, les appels de lecture multimédias peuvent utiliser directement les fichiers du fichier zip sans avoir à le décompresser. Les fichiers multimédias ne doivent pas être compressés quand ils sont ajoutés dans le fichier zip. 

### <a name="filename-format"></a>Format de nom de fichier

Lorsque les fichiers d’extension sont téléchargés, Google Play utilise le schéma suivant pour nommer l’extension :

```
[main|patch].<expansion-version>.<package-name>.obb
```

Les trois composants de ce schéma sont :

- `main`ou `patch` &ndash; Cela spécifie si c’est le fichier principal ou d’extension de patch. Il ne peut y en avoir qu’un seul de chaque.
- `<expansion-version>`&ndash; Il s’agit d’un `versionCode` integer qui correspond à l’APK que le fichier a été associé pour la première fois.
- `<package-name>`&ndash; Il s’agit du nom de paquet Java de l’application.

Par exemple, si la version de l’APK est 21 et le nom du paquet est `mono.samples.helloworld`, le fichier d’extension principale est nommé **main.21.mono.samples.helloworld**.

## <a name="download-process"></a>Processus de téléchargement

Lorsqu’une application est installée à partir de Google Play, les fichiers d’extension doivent être téléchargés et enregistrés avec l’APK. Dans certains cas, cela ne se produit pas ou les fichiers d’extension sont supprimés. Pour gérer cette situation, une application doit vérifier si les fichiers d’extension existent, puis les télécharger, si nécessaire. L’organigramme suivant présente le workflow recommandé de ce processus :

[![Flowchart d’expansion APK](apk-expansion-files-images/apkexpansion.png)](apk-expansion-files-images/apkexpansion.png#lightbox)

Lorsqu’une application démarre, elle doit vérifier que les fichiers d’extension appropriés existent sur l’appareil. Si ce n’est pas le cas, l’application doit faire une demande auprès du service [Application Licensing](https://developer.android.com/google/play/licensing/index.html) de Google Play. Cette vérification est réalisée à l’aide de la *bibliothèque de vérification de licence (License Verification Library, LVL)* et doit être effectuée pour les applications gratuites et les applications sous licence. Cette bibliothèque est principalement utilisée par les applications payantes afin d’appliquer les restrictions de licence. Toutefois, Google a étendu son usage aux bibliothèques d’extension. Les applications gratuites doivent effectuer la vérification LVL, mais peuvent ignorer les restrictions de licence. La demande LVL doit fournir les informations suivantes relatives aux fichiers d’extension requis par l’application : 

- **Taille de fichier **  &ndash; la taille de fichier des fichiers d’extension est utilisée dans le cadre de la vérification qui détermine si les fichiers d’extension corrects ont ou non déjà été téléchargés.
- **Noms de fichiers **  &ndash; il s’agit du nom de fichier (sur l’appareil en cours) sous lequel les packs d’extension doivent être enregistrés.
- **URL de téléchargement **  &ndash; URL qui doit être utilisée pour télécharger les packs d’extension. Elle est unique pour chaque téléchargement et expire quelques instants après avoir été fournie.

Une fois la vérification LVL effectuée, l’application doit télécharger les fichiers d’extension, en prenant en compte les points suivants :

- L’appareil ne dispose peut-être pas de suffisamment d’espace pour stocker les fichiers d’extension.
- Si le Wi-Fi n’est pas disponible, l’utilisateur doit être autorisé à suspendre ou annuler le téléchargement pour éviter des frais de données non souhaités.
- Les fichiers d’extension sont téléchargées en arrière-plan pour éviter de bloquer les interactions de l’utilisateur.
- Pendant le téléchargement en arrière-plan, un indicateur de progression doit être affiché.
- Les erreurs qui se produisent pendant le téléchargement sont gérées et récupérées.

## <a name="architectural-overview"></a>Présentation de l’architecture

Lorsque l’activité principale démarre, elle vérifie que les fichiers d’extension sont téléchargés. Si les fichiers sont téléchargés, leur validité doit être vérifiée.

Si les fichiers d’extension n’ont pas été téléchargées ou si les fichiers actuels ne sont pas valides, les nouveaux fichiers d’extension doivent être téléchargés. Un service borné est créé dans l’application. Lorsque l’activité principale de l’application est démarrée, elle utilise le service borné pour effectuer une vérification dans les services Google Licensing et rechercher le nom des fichiers d’extension et l’URL des fichiers à télécharger. Le service borné télécharge ensuite les fichiers sur un thread d’arrière-plan.

Pour faciliter l’intégration des fichiers d’extension dans une application, Google a créé plusieurs bibliothèques dans Java. Ces bibliothèques sont les suivantes :

- **Bibliothèque du téléchargeur **  &ndash; il s’agit d’une bibliothèque qui facilite l’intégration des fichiers d’extension dans une application. Cette bibliothèque télécharge les fichiers d’extension dans un service en arrière-plan, affiche les notifications utilisateur, gère les problèmes de connectivité réseau, reprend les téléchargements, etc.
- **Bibliothèque de vérification de licence **  &ndash; bibliothèque utilisée pour passer les appels aux services Application Licensing et les traiter. Elle peut également être utilisée pour vérifier les licences afin de savoir si l’utilisation de l’application est autorisée sur l’appareil.
- **Bibliothèque de fichiers zip d’extension d’APK (facultatif) **  &ndash; si les fichiers d’extension sont regroupés dans un fichier zip, cette bibliothèque joue le rôle de fournisseur de contenu en permettant à une application de lire les ressources et les composants directement à partir de l’archive zip sans avoir à la décompresser.

Ces bibliothèques ont été portées vers C# et sont disponibles dans le cadre de la licence Apache 2.0. Pour intégrer rapidement les fichiers d’extension dans une application existante, ces bibliothèques peuvent être ajoutées à une application Xamarin.Android existante. Le code est disponible sur la page [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) de GitHub.
