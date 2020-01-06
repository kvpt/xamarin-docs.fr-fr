---
title: Publication d'une application
ms.prod: xamarin
ms.assetid: 51E19000-040A-2B74-C462-EC57C617085C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 3062afda82be2b45146687b9d77210a42f8f55fa
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487956"
---
# <a name="publishing-an-application"></a>Publication d'une application

Vous avez créé une superbe application et des personnes voudront maintenant l’utiliser. Cette section décrit les étapes de la distribution publique d’une application créée avec Xamarin.Android via des canaux comme les e-mails, un serveur web privé, Google Play ou Amazon Appstore pour Android.

## <a name="overview"></a>Vue d'ensemble de

La dernière étape du développement d’une application Xamarin.Android consiste à publier l’application. La publication est le processus de compilation d’une application Xamarin.Android afin qu’elle soit prête à être installée par les utilisateurs sur leurs appareils. Elle comprend deux tâches essentielles :

- **Préparation de la Publication** &ndash; une version Release de l’application est créée et peut être déployée sur des appareils Android (pour plus d’informations sur la préparation de la mise en production, consultez [préparation d’une application pour la mise](~/android/deploy-test/release-prep/index.md) en production).

- La **distribution** &ndash; la version Release d’une application est mise à disposition via un ou plusieurs des différents canaux de distribution.

Le diagramme suivant illustre les étapes de la publication d’une application Xamarin.Android :

[![Organigramme de génération et déploiement](images/build-and-deploy-steps.png)](images/build-and-deploy-steps.png#lightbox)

Comme le montre le diagramme ci-dessus, la préparation est identique quelle que soit la méthode de distribution utilisée. Une application Android peut être mise à disposition des utilisateurs de différentes manières :

- **Via un site web** &ndash; une application Xamarin. Android peut être mise à disposition en téléchargement sur un site Web, à partir de laquelle les utilisateurs peuvent ensuite installer l’application en cliquant sur un lien.
- **Par courrier électronique** &ndash; il est possible pour les utilisateurs d’installer une application Xamarin. Android à partir de leur adresse de messagerie. L’application est installée lors de l’ouverture de la pièce jointe sur un appareil Android.
- **Via un marché** &ndash; il existe plusieurs places de marché d’applications qui existent pour la distribution, comme [Google Play](https://play.google.com/) ou [Amazon App Store pour Android](https://www.amazon.com/mobile-apps/b?ie=UTF8&node=2350149011) .

La méthode la plus courante pour publier une application consiste à passer par une place de marché connue car elle offre la plus large audience et le meilleur contrôle sur la distribution. Toutefois, cela demande un effort supplémentaire.

Plusieurs canaux peuvent distribuer une application Xamarin.Android simultanément. Par exemple, une application peut être publiée sur Google Play et Amazon Appstore pour Android et être téléchargée à partir d’un serveur web.

Les deux autres méthodes de distribution (téléchargement ou e-mail) se prêtent davantage à la distribution à une partie choisie d’utilisateurs, par exemple un environnement d’entreprise, ou pour une application qui ne s’adresse qu’à un ensemble d’utilisateurs restreint ou bien spécifique.
La distribution par serveur et par e-mail sont également des modèles de publication plus simples, nécessitant moins de préparation.

Le programme de distribution des applications mobiles d’Amazon permet aux développeurs d’applications mobiles de distribuer et de vendre leurs applications sur Amazon. Les utilisateurs peuvent découvrir et acheter des applications sur leur appareil Android à l’aide de l’application Amazon Appstore. Une capture d’écran de l’application Amazon Appstore exécutée sur un appareil Android est affichée ci-dessous :

Google Play est probablement la place de marché la plus complète et la plus populaire pour les applications Android. Google Play permet aux utilisateurs de découvrir, de télécharger, de noter et d’acheter des applications en cliquant sur une seule icône sur leur appareil ou leur ordinateur. Google Play propose également des outils d’analyse des ventes et des tendances du marché, ainsi que des outils de contrôle des appareils et utilisateurs autorisés à télécharger une application. Une capture d’écran de l’application Google Play exécutée sur un appareil Android est affichée ci-dessous :

[![Capture d’écran de Google Play](images/google-play-app.png)](images/google-play-app.png#lightbox)

Cette section montre comment charger l’application sur un App Store tel que Google Play, avec le contenu promotionnel approprié. Un vue d’ensemble conceptuelle des fichiers d’extension APK, ce qu’ils sont et comment ils fonctionnent, est proposée. Les services Google Licensing sont également décrits. Enfin, d’autres moyens de distribution sont présentés, notamment l’utilisation d’un serveur web HTTP, la distribution simple par e-mail et l’Amazon Appstore pour Android.

## <a name="related-links"></a>Liens associés

- [HelloWorldPublishing (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/helloworldpublishing)
- [Processus de génération](~/android/deploy-test/building-apps/build-process.md)
- [Liaison](~/android/deploy-test/linker.md)
- [Récupération d’une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Déployer via Visual Studio App Center](https://docs.microsoft.com/appcenter/distribution/stores/googleplay)
- [Signature d’application](https://source.android.com/security/apksigning/)
- [Publication sur Google Play](https://developer.android.com/distribute/googleplay/publish/index.html)
- [Gestion des licences d’application Google](https://developer.android.com/guide/google/play/licensing/index.html)
- [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary)
- [Portail de distribution des applications mobiles](https://developer.amazon.com/welcome.html)
- [FAQ sur la distribution des applications mobiles Amazon](https://developer.amazon.com/help/faq.html)
