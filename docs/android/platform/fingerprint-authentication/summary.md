---
title: Orientation d’authentification des empreintes digitales
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e955d4f96724bd5682e7d0e6db2c36fa1b7810f4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027429"
---
# <a name="fingerprint-authentication-guidance"></a>Orientation d’authentification des empreintes digitales

## <a name="fingerprint-authentication-guidance"></a>Orientation d’authentification des empreintes digitales

Maintenant que nous avons vu les concepts et les API entourant l’authentification des empreintes digitales Android 6.0, nous allons discuter de quelques conseils généraux pour l’utilisation des API Fingerprint.

1. Utilisez les &ndash; **API de compatibilité Android Support Library v4** Cela simplifiera le code d’application en supprimant la vérification API du code et permettra à une application de cibler le plus d’appareils possible.
2. **Fournir des alternatives à l’authentification** &ndash; d’empreintes digitales Est un excellent moyen rapide pour une application d’authentifier un utilisateur, cependant, on ne peut pas supposer qu’il fonctionnera toujours ou sera disponible. Il est possible que le scanner d’empreintes digitales peut échouer, l’objectif peut-être être sale, l’utilisateur peut ne pas avoir configuré l’appareil pour utiliser l’authentification des empreintes digitales, ou les empreintes digitales ont depuis disparu. Il est également possible que l’utilisateur ne souhaite pas utiliser l’authentification des empreintes digitales avec votre application. Pour ces raisons, une application Android doit fournir un autre processus d’authentification tel que le nom d’utilisateur et le mot de passe.
3. Utilisez &ndash; **l’icône d’empreintes digitales de Google** Toutes les applications doivent utiliser la même icône d’empreinte digitale fournie par Google. L’utilisation d’une icône standard permet aux utilisateurs d’Android de reconnaître facilement où est utilisé l’authentification des empreintes digitales dans les applications : 
    
    ![Icône d’empreinte digitale d’Android](summary-images/ic-fp-40px.png)
    
4. **Notifiez à l’utilisateur** &ndash; Une application doit afficher une sorte de notification à l’utilisateur que le scanner d’empreintes digitales est actif et en attente d’un toucher ou un balayage. 

## <a name="summary"></a>Récapitulatif

L’authentification des empreintes digitales est un excellent moyen de permettre à une application Xamarin.Android de vérifier rapidement les utilisateurs, ce qui facilite l’interaction des utilisateurs avec des fonctionnalités sensibles telles que les achats intégrés. Ce guide a discuté des concepts et du code qui est nécessaire pour incorporer l’API d’empreintes digitales Android 6.0 dans votre application Xamarin.Android.

D’abord, nous avons discuté `FingerprintManager` de `FingerprintManagerCompat`l’empreinte digitale API eux-mêmes, (et ). Nous avons `FingerprintManager.AuthenticationCallbacks` examiné comment la classe abstraite doit être étendue par une application et utilisée comme intermédiaire entre le matériel d’empreintes digitales et l’application elle-même. Ensuite, nous avons examiné comment vérifier l’intégrité `Cipher` des résultats du scanner d’empreintes digitales à l’aide d’un objet Java. Enfin, nous avons touché un peu sur les tests en décrivant comment inscrire une empreinte digitale sur un appareil et en utilisant **une bad** pour simuler un balayage d’empreintes digitales sur un émulateur. 

Si vous ne l’avez pas déjà fait, vous devriez regarder [l’exemple d’application](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) qui accompagne ce guide. [L’échantillon de dialogue d’empreintes digitales](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog) a été porté de Java à Xamarin.Android et fournit un autre exemple sur la façon d’ajouter l’authentification des empreintes digitales à une application Android.

## <a name="related-links"></a>Liens connexes

- [App échantillon de guide d’empreintes digitales](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Échantillon de dialogue d’empreintes digitales](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Icône d’empreinte digitale](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
