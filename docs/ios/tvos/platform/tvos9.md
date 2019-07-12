---
title: Introduction à tvOS 9
description: Cet article présente tous les nouveaux et modifiés API et fonctionnalités disponibles dans tvOS 9 pour les développeurs Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: c4aea5a35fd5db46c9e7ca245b852c988e82f53e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830504"
---
# <a name="introduction-to-tvos-9"></a>Introduction à tvOS 9

_Cet article présente tous les nouveaux et modifiés API et fonctionnalités disponibles dans tvOS 9 pour les développeurs Xamarin.tvOS._

Apple a publié la 4e génération du matériel Apple TV présentant repensé, touch-activer distant, le nouveau système d’exploitation tvOS (basé sur iOS 9) en cours d’exécution.

Pour la première fois, tvOS s’ouvre à la plateforme Apple TV au développeur, ce qui vous permet de créer des applications riches et immersives et publiez-les via le Store d’application intégrée des téléviseurs Apple dans un processus similaire à l’expérience d’écriture et de libération des applications pour iOS à l’aide de l’application iTunes Store.

Si vous êtes familiarisé avec le développement Xamarin.iOS, vous devez rechercher la transition vers tvOS relativement simple. La plupart des API et fonctionnalités est les mêmes, toutefois, de nombreuses API courants ne sont pas disponibles (par exemple, WebKit). En outre, travailler avec les avec la Siri Remote pose des difficultés de conception qui ne sont pas présentes dans l’écran tactile en fonction des appareils iOS.

Ce guide vous donne une introduction à tous les nouveaux et modifiés API et fonctionnalités disponibles dans tvOS 9 pour les développeurs Xamarin.tvOS. Pour plus d’informations sur tvOS, veuillez consulter d’Apple [développement pour le nouveau Apple TV](https://developer.apple.com/tvos/) documentation.

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>Fonctionnalités prises en charge et non pris en charge

applications tvOS en cours d’exécution sur l’Apple TV ont les éléments suivants pris en charge capacités et fonctionnalités :

- App Groups
- Background Modes
- Data Protection
- Game Center
- Contrôleurs de jeu
- iCloud
- Achats dans l’application
- Keychain Sharing

Les fonctionnalités suivantes ne sont pas prises en charge :

- Apple Pay
- App Sandbox
- Associated Domains
- HealthKit
- HomeKit
- Inter-App Audio
- Cartes
- VPN personnel
- Notifications Push
- Wallet
- Configuration d’accessoires sans fil

Consultez notre [pris en charge les assemblys](~/ios/tvos/internals/assemblies.md) et [prise en charge des infrastructures](~/ios/tvos/internals/frameworks.md) documentation pour plus d’informations.

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Apple TV matériel

Le nouveau Apple TV présente les spécifications matérielles suivantes :

- processeur de A8 64 bits
- 32 ou 64 Go de stockage
- 2 Go de RAM
- 10/100Mbps Ethernet
- Wi-Fi 802.11a/b/g/n/ac
- résolution 1080p
- HDMI
- Port C (pour les développeurs et diagnostic uniquement)
- Nouveau Siri Remote ou Apple TV à distance (selon la région)

### <a name="siri-remote"></a>Siri Remote

Selon la région, le fourni Apple TV à distance sera se présentent sous deux configurations d’un : Siri Remote ou Apple TV distant.

Siri distant est actuellement disponible dans les pays suivants :

- Australie
- Canada
- France
- Allemagne
- Japon
- Espagne
- Royaume-Uni
- États-Unis

Tous les autres pays recevra l’Apple TV à distance qui remplace le bouton de Siri avec un bouton de recherche qui affiche l’écran de recherche par défaut avec l’entrée de texte pour la recherche :

[![](tvos9-images/remote02.png "Siri Remote")](tvos9-images/remote02.png#lightbox)

Pour plus d’informations, consultez notre [Siri Remote et Bluetooth contrôleurs](~/ios/tvos/platform/remote-bluetooth.md) documentation.

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Approvisionnement de Apple TV

Tout comme le développement pour iOS, le nouveau tvOS nécessitera le profil de provisionnement approprié pour le développement et distribution basée sur l’appartenance à une équipe et d’identités de signature que vous avez déjà établi auprès d’Apple.

Mise en service approprié est également nécessaire pour accéder aux fonctionnalités de tvOS comme iCloud KVS ou magasins de données de CloudKit. Consultez notre [ressources et du stockage de données](~/ios/tvos/app-fundamentals/resources-data-storage.md) pour plus d’informations sur la prise en charge d’iCloud dans vos applications Xamarin.tvOS.

Profils de provisionnement sont créés et installés de la même façon que de travailler avec des applications Xamarin.iOS. Par conséquent, veuillez consulter notre iOS [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) documentation pour plus d’informations.

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Apple TV applications

Le nouveau matériel Apple TV et tvOS 9 prend en charge deux types d’applications : traditionnels et les applications client-serveur.

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>Applications traditionnelles

Les applications traditionnelles sont achetées via le Store d’application Apple TV et sont installées directement sur l’appareil. Ces applications peuvent être des jeux, des utilitaires ou des applications multimédias qui sont développées avec les mêmes infrastructures et des techniques comme les applications Xamarin.iOS.

Les applications Apple TV ont une taille maximale de 200 Mo et peuvent télécharger les 2 Go de contenu à l’aide de ressources de la demande. Consultez notre [ressources et du stockage de données](~/ios/tvos/app-fundamentals/resources-data-storage.md) pour plus d’informations.

Consultez notre [Hello, tvOS Guide de démarrage rapide](~/ios/tvos/get-started/hello-tvos.md) pour vous familiariser avec les outils et les concepts nécessaires pour développer des applications tvOS à l’aide de Xamarin.tvOS.

<a name="Summary" />

### <a name="client-server-apps"></a>Applications client-serveur

En plus des applications traditionnelles installées, Apple TV rend plus facile de créer un média web-client-serveur de diffusion en continu des applications à l’aide de technologies web (HTTPS, XML et JavaScript). Vous concevez l’interface utilisateur à l’aide du langage de balisage TVML d’Apple et utiliser JavaScript pour définir les comportements de l’application à l’aide de TVMLKit.

Pour plus d’informations, consultez le site d’Apple [référence de langage de balisage Apple TV](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064), [TVJS Framework référence](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), [TVMLKit Framework référence](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [sur HTTP Live Streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) et [HLS de création de spécification pour Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) documentation.

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>Défis d’Interface utilisateur

Contrairement à iOS ou OS X, l’Apple TV n’a pas d’un écran tactile ou une souris qui permettent à l’utilisateur sélectionner directement et d’interagir avec une application ou son contenu. Au lieu de cela ils utilisateur distant Siri nouveau ou un contrôleur de jeu Bluetooth pour naviguer de l’Interface utilisateur d’une application. Pour plus d’informations, consultez notre [Siri Remote et Bluetooth contrôleurs](~/ios/tvos/platform/remote-bluetooth.md) documentation.

En outre, l’expérience utilisateur globale est radicalement différente de celle des applications iOS ou Mac qui ont tendance à être des expériences utilisateur unique. Avec l’Apple TV, les expériences utilisateur ont tendance à être plus sociaux par nature, où plusieurs personnes peuvent être posé sur le canapé interaction avec une seule application et les uns des autres. Pour concevoir une expérience d’application Apple TV réussie (une nouvelle application ou porter un existant), ces modifications doivent prendre en considération. 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>Utilisation de Focus et les Images parallaxe

Comme indiqué ci-dessus, les utilisateurs de votre application Xamarin.tvOS pas d’interagir avec son interface directement en tant qu’avec iOS où ils appuyer sur les images sur l’écran du périphérique, mais indirectement à partir à travers la pièce à l’aide de l’instance distante de Siri. Pour présenter et gérer l’interaction utilisateur, l’Apple TV utilise un modèle de Focus en fonction. 

En tant que le focus change, animations subtiles et des effets (par exemple, l’effet parallaxe sur les images) sont utilisés pour identifier clairement l’élément d’Interface utilisateur qui a actuellement le focus.

Si l’utilisateur effectue un mouvement circulaire et lent Siri à un emplacement distant, l’élément actif sera sway en temps réel en réponse à ce déplacement. Comme le sway se produit, un reflet lumineux est appliquée à son image rendre la surface s’affichent à briller. Après une durée d’inactivité donnée, tout contenu hors du focus s’estompe et l’élément actif augmentera encore plus important.

Pour plus d’informations, consultez notre [fonctionne avec la Navigation et le Focus](~/ios/tvos/app-fundamentals/navigation-focus.md) et [utilisation des icônes et Images](~/ios/tvos/app-fundamentals/icons-images.md) documentation.

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>L’écran d’accueil

L’écran d’accueil de Apple TV montre toutes les applications qui sont installées et fournit un moyen pour accéder aux préférences de l’utilisateur :

[![](tvos9-images/home01.png "L’écran d’accueil")](tvos9-images/home01.png#lightbox)

L’utilisateur accède à une grille des icônes d’application à l’aide des entrées tactiles multipoints distante Siri à l’aide de focus pour sélectionner une application, puis lancez-le. L’icône d’application est le votre première chance de faire une impression excellente sur les utilisateurs potentiels et doit communiquer l’objectif de votre application en un clin de œil.

Chaque application doit fournir une petite et une grande version de son icône de l’application. La petite icône servira à l’écran d’accueil de Apple TV quand l’application est installée. La version grand est utilisée par l’App Store. Icône application de grande taille doit reproduire l’apparence de la version de la petite icône.

Pour plus d’informations, consultez notre [utilisation des icônes et Images](~/ios/tvos/app-fundamentals/icons-images.md) documentation.

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>Le haut de l’étagère

Si l’utilisateur a placé votre application Xamarin.tvOS sur la ligne supérieure sur l’écran d’accueil de Apple TV, une Image d’étagère supérieure volumineux s’affichera lorsque votre application est sélectionnée par l’utilisateur. Cette image doit mettre en évidence les fonctionnalités de votre application ou fournissent des liens directs vers son contenu.

[![](tvos9-images/topshelf01.png "Le haut de l’étagère")](tvos9-images/topshelf01.png#lightbox)

L’Image d’étagère supérieure peut être fourni en tant que statique unique `.png` ou `.lsr` fichier, ou peuvent être créée dynamiquement lors de l’exécution en tant qu’une seule ligne d’éléments pouvant être actif.

Au lieu d’afficher une Image d’étagère supérieure statique, il peut contenir une ligne de dynamique ou les éléments pouvant être actif ou un ensemble dynamique de bannières de défilement. Deux ces style dynamique vous permettent de mettre en surbrillance le contenu fourni par votre application ou d’un saut dans ses fonctionnalités utilisées le plus.

Pour plus d’informations, consultez notre [utilisation des icônes et Images](~/ios/tvos/app-fundamentals/icons-images.md) documentation et Apple [TVServices Framework référence](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) pour plus d’informations sur l’ajout d’une Extension de rayon en haut à votre application pour fournir contenu dynamique étagère de haut.






## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Human Interface Guides](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’application pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Création d’applications pour tvOS avec Xamarin (vidéo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
