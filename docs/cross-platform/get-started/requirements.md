---
title: Configuration système requise
description: Ce document indique la configuration système requise pour générer des applications avec Xamarin sur les ordinateurs Mac et Windows. Il contient également des liens vers des instructions d’installation.
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: davidortinau
ms.author: daortin
ms.date: 10/16/2019
ms.openlocfilehash: 093369010d9327cd2b19fdac09f77697532bfb75
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016436"
---
# <a name="system-requirements"></a>Configuration requise

Les produits Xamarin s’appuient sur les SDK de plateforme Apple et Google pour cibler iOS ou Android, dans le but de s’aligner sur leurs exigences système. Cette page concerne la compatibilité des systèmes avec la plateforme Xamarin. Elle indique quels environnements de développement et versions de SDK utiliser selon le système.

Pour plus d’informations sur l’obtention des logiciels et des SDK nécessaires, passez en revue les [instructions d’installation](#installation-instructions).

## <a name="development-environments"></a>Environnements de développement

Ce tableau liste les combinaisons d’outils de développement et de systèmes d’exploitation que vous pouvez utiliser pour chaque type de plateforme :

[!include[](~/cross-platform/includes/development-environment.md)]

> [!NOTE]
> Pour développer des applications iOS sur des ordinateurs Windows, vous devez disposer d’un [ordinateur Mac accessible sur le réseau](~/ios/get-started/installation/windows/connecting-to-mac/index.md), pour une compilation et un débogage distants. Cela fonctionne également si Visual Studio est exécuté sur une machine virtuelle Windows installée sur un ordinateur Mac.

## <a name="macos-requirements"></a>Configuration requise pour macOS

L’utilisation d’un ordinateur Mac pour le développement Xamarin nécessite les logiciels et SDK suivants. Vérifiez la version de votre système d’exploitation et suivez les instructions relatives au [programme d’installation de Xamarin](#installation-instructions).

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> Xcode peut être installé (et mis à jour) sur [developer.apple.com](https://developer.apple.com/xcode/download/) ou via l’App Store Mac.

### <a name="testing--debugging-on-macos"></a>Test et débogage sur macOS

- Les applications mobiles Xamarin peuvent être déployées sur des appareils physiques par le biais d’une connexion USB à des fins de test et de débogage (les applications Apple Watch doivent être d’abord déployées sur l’iPhone apparié).
- Les applications Xamarin.Mac peuvent être testées directement sur l’ordinateur de développement.

[!include[](~/cross-platform/includes/macos-testing.md)]

> [!WARNING]
> Xamarin.Mac 4.8 prend uniquement en charge macOS 10.9 (Mavericks) ou version ultérieure.
> Les versions précédentes de Xamarin.Mac prenaient en charge macOS 10.7 ou ultérieur, mais l’infrastructure TLS des anciennes versions de macOS n’était pas suffisante pour prendre en charge TLS 1.2. Pour cibler macOS 10.7 ou macOS 10.8, utilisez Xamarin.Mac 4.6 ou antérieur.

## <a name="windows-requirements"></a>Configuration requise pour Windows

L’utilisation d’un ordinateur Windows pour le développement Xamarin nécessite les logiciels et SDK suivants.
Vérifiez la version de votre système d’exploitation. Vérifiez également que vous n’utilisez pas une version *Express* de Visual Studio. Si c’est le cas, effectuez une mise à jour vers l’édition *Community*.
Le programme d’installation de Visual Studio 2019 ou Visual Studio 2017 comporte une option permettant d’installer Xamarin automatiquement (la charge de travail **Développement mobile en .NET**).

[!include[](~/cross-platform/includes/windows-requirements.md)]

> [!NOTE]
>
> - Xamarin pour Visual Studio prend en charge toutes les éditions de Visual Studio 2019 ou Visual Studio 2017 (Community, Professional et Enterprise).
> - Pour utiliser les derniers kits de développement logiciel (SDK) Android et iOS, vous devez disposer de la dernière version de Visual Studio. Pour des exigences de version spécifiques, reportez-vous aux notes de [publication de Xamarin. Android](/xamarin/android/release-notes/) et aux [notes de publication de Xamarin. iOS](/xamarin/ios/release-notes/).
> - Si vous souhaitez développer des applications Xamarin.Forms pour la plateforme Windows universelle (UWP), vous devez utiliser Windows 10 avec Visual Studio 2017. Visual Studio 2019 est recommandé.

### <a name="testing--debugging-on-windows"></a>Test et débogage sur Windows

Les applications mobiles Xamarin peuvent être déployées sur des appareils physiques par le biais d’une connexion USB ou sans fil à des fins de test et de débogage (les appareils iOS doivent être connectés à l’ordinateur Mac, et non à l’ordinateur qui exécute Visual Studio).

[!include[](~/cross-platform/includes/windows-testing.md)]

## <a name="installation-instructions"></a>Instructions d’installation

Vous pouvez télécharger la dernière version de Xamarin pour Mac OS avec [Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/installation). Pour Windows, suivez les [instructions d’installation de Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

La liste complète des versions actuelles de nos produits est disponible dans la [page des nouveautés](~/whats-new/index.yml). Cette page contient également des liens vers les notes de publication.

Les instructions d’[installation](~/get-started/installation/index.md) de chaque plateforme sont disponibles ici :

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

Vous pouvez également consulter des informations supplémentaires concernant les [exigences Xamarin.Forms et les plateformes prises en charge](~/get-started/requirements.md).

## <a name="related-links"></a>Liens connexes

- [Télécharger Xamarin](https://visualstudio.microsoft.com/xamarin/)
- [Notes de publication de Xamarin.Forms](/xamarin/xamarin-forms/release-notes/)
- [Notes de publication de Xamarin.Android](/xamarin/android/release-notes/)
- [Notes de publication de Xamarin.iOS](/xamarin/ios/release-notes/)
