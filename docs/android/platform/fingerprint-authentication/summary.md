---
title: Aide sur l’authentification par empreinte digitale
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: aaa67972afeffd10c038a145a5703e917647b0fb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453868"
---
# <a name="fingerprint-authentication-guidance"></a>Aide sur l’authentification par empreinte digitale

## <a name="fingerprint-authentication-guidance"></a>Aide sur l’authentification par empreinte digitale

Maintenant que nous avons vu les concepts et les API entourant l’authentification par empreinte digitale Android 6,0, nous allons aborder quelques conseils généraux sur l’utilisation des API d’empreinte digitale.

1. **Utiliser les API de compatibilité v4 de la bibliothèque de prise en charge Android** &ndash; Cela simplifie le code de l’application en supprimant la vérification de l’API du code et en permettant à une application de cibler le plus grand nombre d’appareils possible.
2. **Fournir des alternatives à l’authentification par** &ndash; empreinte digitale L’authentification par empreinte digitale est un moyen formidable et rapide pour une application d’authentifier un utilisateur. Toutefois, il ne peut pas être supposé qu’elle fonctionnera toujours ou sera disponible. Il est possible que le scanneur d’empreintes digitales échoue, que l’objectif soit incorrect, que l’utilisateur n’ait pas configuré l’appareil pour utiliser l’authentification par empreinte digitale, ou que les empreintes digitales n’aient pas disparu. Il est également possible que l’utilisateur ne souhaite pas utiliser l’authentification par empreinte digitale avec votre application. Pour ces raisons, une application Android doit fournir un autre processus d’authentification, tel que le nom d’utilisateur et le mot de passe.
3. **Utiliser l’icône** &ndash; d’empreintes de Google Toutes les applications doivent utiliser la même icône d’empreinte digitale fournie par Google. L’utilisation d’une icône standard permet aux utilisateurs Android de reconnaître facilement où l’authentification par empreinte digitale des applications est utilisée : 
    
    ![Icône d’empreinte digitale Android](summary-images/ic-fp-40px.png)
    
4. **Notifier l’utilisateur** &ndash; Une application doit afficher un type de notification à l’utilisateur que le scanneur d’empreintes digitales est actif et qu’il attend une pression tactile ou un balayage. 

## <a name="summary"></a>Résumé

L’authentification par empreinte digitale est un excellent moyen d’autoriser une application Xamarin. Android à vérifier rapidement les utilisateurs, ce qui permet aux utilisateurs d’interagir plus facilement avec des fonctionnalités sensibles, telles que des achats dans l’application. Ce guide a présenté les concepts et le code nécessaires pour incorporer les API d’empreinte digitale Android 6,0 dans votre application Xamarin. Android.

Tout d’abord, nous avons abordé les API d’empreintes digitales, `FingerprintManager` (et `FingerprintManagerCompat` ). Nous avons examiné comment la `FingerprintManager.AuthenticationCallbacks` classe abstraite doit être étendue par une application et utilisée comme intermédiaire entre le matériel d’empreinte digitale et l’application elle-même. Ensuite, nous avons examiné comment vérifier l’intégrité des résultats de l’analyseur d’empreintes digitales à l’aide d’un `Cipher` objet Java. Enfin, nous avons abordé un peu sur les tests en décrivant comment inscrire une empreinte digitale sur un appareil et en utilisant **ADB** pour simuler un balayage par empreinte digitale sur un émulateur. 

Si vous ne l’avez pas déjà fait, consultez l' [exemple d’application](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) qui accompagne ce guide. L' [exemple de boîte de dialogue empreinte digitale](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog) a été porté de Java vers Xamarin. Android et fournit un autre exemple sur l’ajout de l’authentification par empreinte digitale à une application Android.

## <a name="related-links"></a>Liens associés

- [Exemple d’application Guide d’empreinte digitale](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Exemple de boîte de dialogue empreinte digitale](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Icône d’empreinte digitale](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)