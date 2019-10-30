---
title: Aide sur l’authentification par empreinte digitale
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e955d4f96724bd5682e7d0e6db2c36fa1b7810f4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027429"
---
# <a name="fingerprint-authentication-guidance"></a>Aide sur l’authentification par empreinte digitale

## <a name="fingerprint-authentication-guidance"></a>Aide sur l’authentification par empreinte digitale

Maintenant que nous avons vu les concepts et les API entourant l’authentification par empreinte digitale Android 6,0, nous allons aborder quelques conseils généraux sur l’utilisation des API d’empreinte digitale.

1. **Utilisez les API de compatibilité v4 de la bibliothèque de prise en charge Android** &ndash; cela simplifie le code de l’application en supprimant la vérification de l’API du code et en permettant à une application de cibler le plus grand nombre d’appareils possible.
2. **Fournir des alternatives à l’authentification par empreinte digitale** &ndash; l’authentification par empreinte digitale est un moyen formidable et rapide pour une application d’authentifier un utilisateur. Toutefois, il n’est pas possible de supposer qu’elle fonctionnera toujours ou sera disponible. Il est possible que le scanneur d’empreintes digitales échoue, que l’objectif soit incorrect, que l’utilisateur n’ait pas configuré l’appareil pour utiliser l’authentification par empreinte digitale, ou que les empreintes digitales n’aient pas disparu. Il est également possible que l’utilisateur ne souhaite pas utiliser l’authentification par empreinte digitale avec votre application. Pour ces raisons, une application Android doit fournir un autre processus d’authentification, tel que le nom d’utilisateur et le mot de passe.
3. **Utilisez l’icône d’empreinte digitale de google** &ndash; toutes les applications doivent utiliser la même icône d’empreinte digitale fournie par Google. L’utilisation d’une icône standard permet aux utilisateurs Android de reconnaître facilement où l’authentification par empreinte digitale des applications est utilisée : 
    
    ![Icône d’empreinte digitale Android](summary-images/ic-fp-40px.png)
    
4. **Notifier l’utilisateur** &ndash; une application doit afficher un message indiquant à l’utilisateur que le scanneur d’empreintes digitales est actif et en attente d’une pression tactile ou d’un balayage. 

## <a name="summary"></a>Récapitulatif

L’authentification par empreinte digitale est un excellent moyen d’autoriser une application Xamarin. Android à vérifier rapidement les utilisateurs, ce qui permet aux utilisateurs d’interagir plus facilement avec des fonctionnalités sensibles, telles que des achats dans l’application. Ce guide a présenté les concepts et le code nécessaires pour incorporer les API d’empreinte digitale Android 6,0 dans votre application Xamarin. Android.

Tout d’abord, nous avons abordé les API d’empreintes digitales, `FingerprintManager` (et `FingerprintManagerCompat`). Nous avons examiné comment la classe abstraite `FingerprintManager.AuthenticationCallbacks` doit être étendue par une application et utilisée comme intermédiaire entre le matériel d’empreinte digitale et l’application elle-même. Ensuite, nous avons examiné comment vérifier l’intégrité des résultats de l’analyseur d’empreintes digitales à l’aide d’un objet Java `Cipher`. Enfin, nous avons abordé un peu sur les tests en décrivant comment inscrire une empreinte digitale sur un appareil et en utilisant **ADB** pour simuler un balayage par empreinte digitale sur un émulateur. 

Si vous ne l’avez pas déjà fait, consultez l' [exemple d’application](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) qui accompagne ce guide. L' [exemple de boîte de dialogue empreinte digitale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog) a été porté de Java vers Xamarin. Android et fournit un autre exemple sur l’ajout de l’authentification par empreinte digitale à une application Android.

## <a name="related-links"></a>Liens associés

- [Exemple d’application Guide d’empreinte digitale](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Exemple de boîte de dialogue empreinte digitale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Icône d’empreinte digitale](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
