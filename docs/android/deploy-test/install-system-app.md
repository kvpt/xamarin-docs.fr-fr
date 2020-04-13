---
title: Installation de Xamarin.Android comme application système
description: Ce guide traite des différences entre une application système et une application utilisateur et indique comment installer une application Xamarin.Android comme application système. Ce guide s’applique aux auteurs d’images ROM Android personnalisées. Il n’explique pas comment créer un ROM personnalisé.
ms.prod: xamarin
ms.assetid: 0113143B-7D8D-4C4C-B2F5-B966A2E7CE1F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 72cddde86708b5573dc578165354d137c4dc35b6
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76723898"
---
# <a name="installing-xamarinandroid-as-a-system-app"></a>Installation de Xamarin.Android comme application système

_Ce guide discutera des différences entre une application système et une application utilisateur, et comment installer une application Xamarin.Android comme une application système. Ce guide s’applique aux auteurs d’images Android ROM personnalisées. Il n’expliquera pas comment créer un ROM personnalisé._

## <a name="system-app"></a>Application système

Les auteurs des images ROM Android personnalisées ou les fabricants d’appareils Android peuvent souhaiter inclure une application Xamarin.Android comme _application système_ lors de la distribution d’un ROM ou d’un périphérique. Une application système est une application considérée comme étant importante pour le fonctionnement de l’appareil ou qui fournit des fonctionnalités que l’auteur du ROM personnalisé souhaite toujours disponibles.

Les applications système sont installées dans le dossier **/System/application/** (un répertoire en lecture seule sur le système de fichiers) et ne peuvent pas être supprimées ni déplacées par l’utilisateur, à moins que l’utilisateur ait accès à la racine. En revanche, une application installée par l’utilisateur (généralement à partir de Google Play ou en procédant au chargement indépendant de l’application) est appelée _application utilisateur_. Les applications utilisateur peuvent être supprimées par l’utilisateur et dans de nombreux cas elles peuvent être déplacées vers un autre emplacement sur l’appareil (par exemple, un type de stockage externe).

Les applications système se comportent exactement comme les applications de l’utilisateur, mais ont les exceptions notables suivantes :

- Les applications système peuvent être mises à niveau, à l’instar d’une _application utilisateur_ normale. Toutefois, puisqu’il existe toujours une copie de l’application dans **/System/application/**, il est toujours possible de restaurer l’application à la version d’origine.

- Certaines autorisations système uniquement qui ne sont pas disponibles pour une application utilisateur peuvent être accordées à des applications système. Un exemple d’une autorisation [`BLUETOOTH_PRIVILEGED`](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED)système uniquement est , qui permet aux applications de jumeler avec des périphériques Bluetooth sans aucune interaction utilisateur.

Il est possible de distribuer une application Xamarin.Android comme application système. En plus de fournir un APK à la ROM personnalisée, il existe deux bibliothèques partagées, **libmonodroid.so** et **libmonosgen-2.0.so** qui doivent être copiées manuellement à partir de APK pour le système de fichiers de l’image ROM. Ce guide explique les étapes à suivre.

## <a name="restrictions"></a>Restrictions

Ce guide s’applique aux auteurs d’images ROM Android personnalisées. Il n’explique pas comment créer un ROM personnalisé.

Ce guide suppose une connaissance de [l’empaquetage d’un APK de mise en production pour un Xamarin.Android](~/android/deploy-test/publishing/index.md) et une compréhension des [Architectures d’UC](~/android/app-fundamentals/cpu-architectures.md) pour les applications Android.

## <a name="install-a-xamarinandroid-app-as-a-system-app"></a>Installation d’une application Xamarin.Android comme application système

Les étapes suivantes décrivent comment installer une application Xamarin.Android comme application système.

1. **Empaquetage d’un APK de mise en production de l’application Xamarin.Android** &ndash; Décrit plus en détail dans le guide [Publication d’une application](~/android/deploy-test/publishing/index.md).

2. **Extraire des bibliothèques partagées de l’APK** &ndash;À l’aide de n’importe quel programme utilitaire ZIP, ouvrez le fichier APK et examinez le contenu du dossier **/lib/**. Ce dossier aura un sous-répertoire pour chaque _interface binaire d’application_ (ABI) prise en charge par l’application ; le contenu de ce dossier inclut toutes les bibliothèques partagées requises par l’application sur cette ABI en particulier :

    ![Capture d’écran de fichiers .so dans le dossier armeabi-v7a de taskypro.zip](install-system-app-images/install-system-app-01.png)

   Dans la capture d’écran précédente, une seule ABI est prise en charge (**armeabi-v7a**) contenant les deux fichiers **.so** requis par l’application. Notez qu’il est uniquement nécessaire d’extraire les fichiers ABI appropriés pour l’appareil ou l’architecture cible du ROM de l’appareil, autrement dit, ne copiez pas les fichiers **.so** à partir du dossier **x86** vers un appareil ou un ROM ** armeabi-v7a**.

3. **Copiez les fichiers .so dans /system/lib** &ndash; Copiez les fichiers **.so** extraits de l’APK dans l’étape précédente dans le dossier **/system/lib/** sur le ROM personnalisé.

4. **Copier le fichier APK sur /system/app** &ndash; L’étape finale consiste à copier le fichier APK dans le dossier **/system/app** sur le ROM.

## <a name="summary"></a>Récapitulatif

Ce guide traite de la différence entre une _application système_ et une _application utilisateur_ et explique comment installer une application Xamarin.Android comme application système.

## <a name="related-links"></a>Liens connexes

- [Publication d'une application](~/android/deploy-test/publishing/index.md)
- [Architectures d’UC](~/android/app-fundamentals/cpu-architectures.md)
- [BLUETOOTH_PRIVILEGED](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED)
- [Gestion de l’ABI](https://developer.android.com/ndk/guides/abis)
