---
title: Mise à jour de votre application avec iOS 11
description: Ce document contient des liens vers divers guides qui décrivent les nouvelles fonctionnalités disponibles pour les développeurs Xamarin. iOS avec la sortie d’iOS 11. Par exemple, les mises à jour de la conception visuelle, les modifications de l’App Store et les mises à jour de l’icône de l’application.
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 3f331c38d470ba77de7b3fc4ef0143bb673683e8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286339"
---
# <a name="updating-your-app-to-ios-11"></a>Mise à jour de votre application avec iOS 11

Dans iOS 11, Apple a introduit des mises à jour d’architecture, de nouvelles modifications visuelles et un processus iTunes Connect mis à jour. Ce guide explore chacune de ces modifications pour vous aider à mettre à jour votre application Xamarin. iOS pour iOS 11.

## <a name="architecture-changesarchitecture-changesmd"></a>[Changements dans l'architecture](architecture-changes.md)

L’une des modifications les plus importantes que vous devez connaître avec iOS 11 est la désapprobation de la prise en charge de 32 bits pour les applications, comme indiqué dans le communiqué de presse [d’Apple](https://developer.apple.com/news/?id=06282017b) .

Ce guide vous guide tout au long de la mise à jour de votre application pour 64 bits.

## <a name="visual-design-updatesvisual-designmd"></a>[Mises à jour de la conception visuelle](visual-design.md)

Dans iOS 11, Apple a introduit de nouvelles modifications visuelles, notamment des mises à jour de la barre de navigation, de la barre de recherche et des vues de table. En outre, des améliorations ont été apportées pour offrir une plus grande flexibilité sur les marges et le contenu en plein écran. Ces modifications sont traitées dans ce guide.

## <a name="app-store-changesapp-store-changesmd"></a>[Changements dans la conception visuelle](app-store-changes.md)

L’App Store iOS a été entièrement repensée, ce qui permet non seulement aux utilisateurs de naviguer efficacement dans le Store, mais également, en tant que développeur, de promouvoir votre application pour les utilisateurs. Ces promotions incluent les mises à jour des achats dans l’application et les mises à jour de la page du produit. iOS 11 ajoute également des mises à jour concernant la communication avec les utilisateurs, l’ajout de votre application icône et la publication de votre application sur le public.

## <a name="app-icon-updates"></a>Mises à jour des icônes d’application

> [!NOTE]
> Les icônes d’application doivent maintenant être fournies par un _catalogue de composants_. 

Pour plus d’informations sur l’utilisation des catalogues de ressources, consultez le Guide de l' [icône App Store](~/ios/app-fundamentals/images-icons/app-store-icon.md) . Pour obtenir de l’aide sur la migration d’icônes d’un fichier info. plist vers un catalogue de ressources, consultez le guide [migration à partir d’info. plist vers les catalogues de ressources](~/ios/app-fundamentals/images-icons/app-icons.md) .

L’icône requise dans le catalogue de composants est nommée **App Store** et doit avoir une taille de 1024 x 1024. Apple indique que l’icône visible dans l’App Store et qui se trouve dans le catalogue de ressources ne peut pas être transparente ni contenir de canal alpha.

![Emplacement de l’icône App Store dans le catalogue de ressources.](images/image1.png)

## <a name="related-links"></a>Liens associés

- [Nouveautés d’iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Page produit App Store mise à jour (Apple)](https://developer.apple.com/app-store/product-page/)
- [Mise à jour de votre application pour iOS 11 (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/204/)
