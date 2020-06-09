---
title: Introduction à tvOS 9
description: Cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans tvOS 9 pour les développeurs Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 99565d7e61e8e8c1d28b67a6a6d25979c79fa387
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566160"
---
# <a name="introduction-to-tvos-9"></a>Introduction à tvOS 9

_Cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans tvOS 9 pour les développeurs Xamarin. tvOS._

Apple a publié la 4e génération de matériel Apple TV avec une version distante, avec activation tactile, qui exécute le nouveau système d’exploitation tvOS (basé sur iOS 9).

Pour la première fois, tvOS ouvre la plateforme Apple TV pour le développeur, ce qui vous permet de créer des applications riches et immersifs et de les diffuser via l’App Store intégré d’Apple TV dans un processus similaire à l’expérience d’écriture et de publication d’applications pour iOS à l’aide de l’iTunes App Store.

Si vous êtes familiarisé avec le développement Xamarin. iOS, vous devriez trouver la transition vers tvOS relativement simple. La plupart des API et des fonctionnalités sont identiques, cependant, de nombreuses API courantes ne sont pas disponibles (par exemple, WebKit). En outre, l’utilisation du avec la télécommande Siri pose des problèmes de conception qui ne sont pas présents dans les appareils iOS basés sur un écran tactile.

Ce guide vous donnera une présentation de toutes les API et fonctionnalités nouvelles et modifiées disponibles dans tvOS 9 pour les développeurs Xamarin. tvOS. Pour plus d’informations sur tvOS, consultez développement d’Apple [pour la nouvelle documentation Apple TV](https://developer.apple.com/tvos/) .

<a name="Supported-and-Unsupported-Capabilities"></a>

## <a name="supported-and-unsupported-capabilities"></a>Fonctionnalités prises en charge et non prises en charge

les applications tvOS qui s’exécutent sur Apple TV offrent les fonctionnalités et fonctionnalités prises en charge suivantes :

- App Groups
- Background Modes
- Data Protection
- Centre de jeux
- Contrôleurs de jeu
- iCloud
- Achats dans l’application
- Keychain Sharing

Les fonctionnalités et les fonctionnalités suivantes ne sont pas prises en charge :

- Apple Pay
- Sandbox d’application
- Associated Domains
- HealthKit
- HomeKit
- Inter-App Audio
- Cartes
- VPN personnel
- Notifications Push
- Wallet
- Configuration d’accessoires sans fil

Pour plus d’informations, consultez nos [assemblys pris en charge](~/ios/tvos/internals/assemblies.md) et la documentation sur les [frameworks pris en charge](~/ios/tvos/internals/frameworks.md) .

<a name="Apple-TV-Hardware"></a>

## <a name="apple-tv-hardware"></a>Matériel Apple TV

La nouvelle TV Apple présente les spécifications matérielles suivantes :

- processeur a8 64 bits
- 32 Go ou 64 Go de stockage
- 2 Go de RAM
- Ethernet 10/100 Mbits/s
- WiFi 802.11 a/b/g/n/AC
- résolution de 1080p
- HDMI
- Port USB C (pour les développeurs et les diagnostics uniquement)
- Nouvelle télécommande Siri ou Apple TV à distance (basée sur la région)

### <a name="siri-remote"></a>Siri à distance

En fonction de la région, la télécommande Apple TV fournie se trouve dans l’une des configurations suivantes : Siri Remote ou Apple TV Remote.

La télécommande Siri est actuellement disponible dans les pays suivants :

- Australie
- Canada
- France
- Allemagne
- Japon
- Espagne
- Royaume-Uni
- États-Unis

Tous les autres pays recevront la télécommande Apple TV qui remplace le bouton Siri par un bouton de recherche qui affiche l’écran de recherche par défaut avec entrée de texte pour la recherche :

[![](tvos9-images/remote02.png "Siri Remote")](tvos9-images/remote02.png#lightbox)

Pour plus d’informations, consultez notre documentation sur [les contrôleurs distants et Bluetooth Siri](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="Apple-TV-Provisioning"></a>

## <a name="apple-tv-provisioning"></a>Approvisionnement Apple TV

Tout comme le développement pour iOS, le nouveau tvOS nécessitera le profil de provisionnement approprié pour le développement et la distribution en fonction de l’appartenance de l’équipe et des identités de signature que vous avez déjà établies avec Apple.

Une configuration appropriée est également nécessaire pour accéder aux fonctionnalités tvOS telles que les magasins de données iCloud KVS ou CloudKit. Pour plus d’informations sur la prise en charge de iCloud dans vos applications Xamarin. tvOS, consultez nos [ressources et le stockage de données](~/ios/tvos/app-fundamentals/resources-data-storage.md) .

Les profils de provisionnement sont créés et installés de la même façon que pour l’utilisation des applications Xamarin. iOS. Pour plus d’informations, consultez notre documentation sur l' [approvisionnement d’appareils](~/ios/get-started/installation/device-provisioning/index.md) iOS.

<a name="Apple-TV-Apps"></a>

## <a name="apple-tv-apps"></a>Applications de TV Apple

Le nouveau matériel Apple TV et tvOS 9 prennent en charge deux types d’applications : les applications traditionnelles et client-serveur.

<a name="Traditional-Apps"></a>

### <a name="traditional-apps"></a>Applications traditionnelles

Les applications traditionnelles sont achetées à partir du magasin d’applications Apple TV et sont installées directement sur l’appareil. Ces applications peuvent être des jeux, des utilitaires ou des applications multimédias développés à l’aide des mêmes infrastructures et techniques que les applications Xamarin. iOS.

Les applications Apple TV ont une taille maximale de 200 Mo et peuvent télécharger des 2 Go supplémentaires de contenu à l’aide des ressources à la demande. Pour plus d’informations, consultez nos [ressources et le stockage de données](~/ios/tvos/app-fundamentals/resources-data-storage.md) .

Consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md) pour vous familiariser avec les outils et les concepts nécessaires au développement d’applications tvOS à l’aide de Xamarin. tvOS.

<a name="Summary"></a>

### <a name="client-server-apps"></a>Applications client-serveur

En plus des applications traditionnelles installées, Apple TV vous permet de créer facilement des applications Web de streaming de média client-serveur à l’aide de technologies Web (HTTPs, XML et JavaScript). Vous allez concevoir l’interface utilisateur à l’aide du langage de balisage TVML d’Apple et utiliser JavaScript pour définir les comportements de l’application à l’aide de TVMLKit.

Pour plus d’informations, consultez les informations de référence sur le [langage de balisage Apple](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064)pour Apple, [TVJS Framework Reference](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), [TVMLKit Framework reference](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [à propos de http live streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) et [de la spécification de création TLS pour la documentation Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) .

<a name="User-Interface-Challenges"></a>

## <a name="user-interface-challenges"></a>Défis liés à l’interface utilisateur

Contrairement à iOS ou OS X, Apple TV ne dispose pas d’un écran tactile ou d’une souris permettant à l’utilisateur de sélectionner et d’interagir directement avec une application ou son contenu. Au lieu de cela, ils ont le nouveau Siri distant ou un contrôleur de jeu Bluetooth pour naviguer dans l’interface utilisateur d’une application. Pour plus d’informations, consultez notre documentation sur [les contrôleurs distants et Bluetooth Siri](~/ios/tvos/platform/remote-bluetooth.md) .

En outre, l’expérience utilisateur globale est radicalement différente des applications iOS ou Mac qui ont tendance à être des expériences utilisateur uniques. Avec Apple TV, les expériences utilisateur ont tendance à être plus sociales, où plusieurs personnes peuvent être assises sur le canapé qui interagit avec une application unique et les unes des autres. Pour concevoir une expérience d’application d’Apple TV réussie (une nouvelle application ou le portage d’une application existante), ces modifications doivent être prises en considération. 

<a name="Summary"></a>

### <a name="working-with-focus-and-parallax-images"></a>Utilisation d’images focus et parallaxe

Comme indiqué ci-dessus, les utilisateurs de votre application Xamarin. tvOS n’interagissent pas avec son interface directement comme avec iOS, où ils appuient sur des images sur l’écran de l’appareil, mais indirectement de la salle à l’aide de la télécommande Siri. Pour présenter et gérer cette interaction avec l’utilisateur, Apple TV utilise un modèle de focus. 

À mesure que le focus change, des animations et des effets subtils (tels que l’effet parallaxe sur les images) sont utilisés pour identifier clairement l’élément d’interface utilisateur qui a actuellement le focus.

Si l’utilisateur effectue un mouvement circulaire lent sur le Siri à distance, l’élément ayant le focus est Sway en temps réel en réponse à ce mouvement. Au fur et à mesure que le Sway se produit, un reflet éclairé est appliqué à son image, ce qui rend l’aire sembler éclatée. Après une quantité donnée d’inactivité, tout contenu hors focus est estompé et l’élément ayant le focus augmente encore plus.

Pour plus d’informations, consultez notre page [utilisation de la navigation et du focus](~/ios/tvos/app-fundamentals/navigation-focus.md) et [utilisation de la documentation sur les icônes et les images](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="The-Home-Screen"></a>

### <a name="the-home-screen"></a>Écran d’accueil

L’écran d’accueil Apple TV affiche toutes les applications installées et fournit un moyen d’accéder aux préférences de l’utilisateur :

[![](tvos9-images/home01.png "The Home Screen")](tvos9-images/home01.png#lightbox)

L’utilisateur parcourt une grille d’icônes d’application à l’aide de gestes tactiles sur le Siri distant à l’aide du focus pour sélectionner une application et la lancer. L’icône de l’application est la première opportunité de vous donner une grande impression à votre utilisateur potentiel et doit communiquer d’un coup d’œil à l’objectif de votre application.

Chaque application doit fournir une petite et une grande version de son icône d’application. La petite icône sera utilisée sur l’écran d’accueil Apple TV lorsque l’application est installée. La version volumineuse est utilisée par l’App Store. L’icône d’application de grande taille doit imiter l’apparence de la petite version de l’icône.

Pour plus d’informations, consultez notre documentation [sur l’utilisation des icônes et des images](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="The-Top-Shelf"></a>

### <a name="the-top-shelf"></a>L’étagère supérieur

Si l’utilisateur a placé votre application Xamarin. tvOS sur la ligne supérieure de l’écran d’accueil Apple TV, une grande image de haut rayon s’affiche lorsque votre application est sélectionnée par l’utilisateur. Cette image doit mettre en surbrillance les fonctionnalités de votre application ou fournir des liens directs vers son contenu.

[![](tvos9-images/topshelf01.png "The Top Shelf")](tvos9-images/topshelf01.png#lightbox)

L’image de l’étagère supérieure peut être fournie sous la forme d’un `.png` fichier statique ou d’un fichier unique, `.lsr` ou elle peut être créée dynamiquement au moment de l’exécution sous la forme d’une ligne unique d’éléments pouvant être mis sur le focus.

Au lieu d’afficher une image de rayon supérieure statique, elle peut contenir une ligne dynamique ou des éléments pouvant être activés ou un ensemble dynamique de bannières de défilement. Ces deux styles dynamiques vous permettent de mettre en surbrillance le contenu fourni par votre application ou d’accéder à ses fonctionnalités les plus utilisées.

Pour plus d’informations, consultez notre documentation sur l' [utilisation des icônes et des images](~/ios/tvos/app-fundamentals/icons-images.md) et informations de référence sur l' [infrastructure TVServices](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) d’Apple pour plus d’informations sur l’ajout d’une extension d’étagère supérieure à votre application afin de fournir un contenu dynamique supérieur.

## <a name="related-links"></a>Liens connexes

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Création d’applications pour tvOS avec Xamarin (vidéo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
