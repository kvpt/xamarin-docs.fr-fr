---
title: Implémentation d’un lecteur vidéo
description: Cet article explique comment implémenter une application de lecteur vidéo à l’aide de Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 0CE9BEE7-4F81-4A00-B9B3-5E2535CD3050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 08bfb86f040bfbce834df5a5d98231afae92e78d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84133757"
---
# <a name="implementing-a-video-player"></a>Implémentation d’un lecteur vidéo

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Il est parfois souhaitable de lire des fichiers vidéo dans une Xamarin.Forms application. Cette série d’articles explique comment écrire des convertisseurs personnalisés pour iOS, Android et la plateforme Windows universelle (UWP) pour une Xamarin.Forms classe nommée `VideoPlayer` .

Dans l’exemple [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos), tous les fichiers qui implémentent et prennent en charge `VideoPlayer` sont dans des dossiers nommés `FormsVideoLibrary` et identifiés avec l’espace de noms `FormsVideoLibrary` ou des espaces de noms qui commencent par `FormsVideoLibrary`. Cette organisation et ce nom doivent faciliter la copie des fichiers de lecteur vidéo dans votre propre Xamarin.Forms solution.

`VideoPlayer` peut lire des fichiers vidéo de trois types de sources :

- Internet à l’aide d’une URL
- Une ressource incorporée dans l’application de plateforme
- La vidéothèque de l’appareil

Les lecteurs vidéo nécessitent des *contrôles de transport*, qui sont des boutons de lecture et de pause de la vidéo, et une barre de positionnement qui montre la progression de la vidéo et permet à l’utilisateur de passer rapidement à un autre emplacement. `VideoPlayer` peut utiliser les contrôles de transport et la barre de positionnement fournis par la plateforme (comme montré ci-dessous), mais vous pouvez aussi fournir des contrôles de transport et une barre de positionnement personnalisés. Voici le programme exécuté sur iOS, Android et la plateforme Windows universelle :

[![Lire une vidéo Web](web-videos-images/playwebvideo-small.png "Lire une vidéo Web")](web-videos-images/playwebvideo-large.png#lightbox "Lire une vidéo Web")

Bien entendu, vous pouvez faire pivoter le téléphone sur le côté pour agrandir la vue.

Un lecteur vidéo plus sophistiqué a des fonctionnalités supplémentaires, comme le contrôle du volume, un mécanisme d’interruption de la vidéo en cas d’appel téléphonique et un moyen de garder l’écran actif pendant la lecture.

La série d’articles suivante montre progressivement la façon dont sont générés les convertisseurs de plateforme et les classes sous-jacentes :

## <a name="creating-the-platform-video-players"></a>[Création des lecteurs vidéo de la plateforme](player-creation.md)

Chaque plateforme nécessite une classe `VideoPlayerRenderer` qui crée et gère un contrôle de lecteur vidéo pris en charge par la plateforme. Cet article décrit la structure des classes de convertisseur et la façon dont les lecteurs sont créés.

## <a name="playing-a-web-video"></a>[Visionner une vidéo Web](web-videos.md)

Internet est probablement la source de vidéos la plus courante d’un lecteur vidéo. Cet article décrit comment une vidéo web peut être référencée et utilisée comme source du lecteur vidéo.

## <a name="binding-video-sources-to-the-player"></a>[Liaison de sources vidéo au lecteur](source-bindings.md)

Cet article utilise un `ListView` pour présenter une collection de vidéos à lire. Un programme montre comment le fichier code-behind peut définir la source vidéo du lecteur vidéo, mais un deuxième programme montre comment vous pouvez utiliser la liaison de données entre le `ListView` et le lecteur vidéo.

## <a name="loading-application-resource-videos"></a>[Chargement des vidéos des ressources d’application](loading-resources.md)

Les vidéos peuvent être incorporées comme des ressources dans les projets de plateforme. Cet article décrit comment stocker ces ressources et les charger par la suite dans le programme pour qu’elles soient lues par le lecteur vidéo.

## <a name="accessing-the-devices-video-library"></a>[Accès à la bibliothèque vidéo de l’appareil](accessing-library.md)

Quand vous créez une vidéo avec l’appareil photo de l’appareil, le fichier vidéo est stocké dans la bibliothèque d’images de l’appareil. Cet article explique comment accéder au sélecteur d’images de l’appareil pour sélectionner la vidéo et la lire à l’aide du lecteur vidéo.

## <a name="custom-video-transport-controls"></a>[Contrôles de transport vidéo personnalisés](custom-transport.md)

Bien que les lecteurs vidéo de chaque plateforme fournissent leurs propres contrôles de transport sous la forme de boutons pour **Lire** et **Mettre en pause**, vous pouvez supprimer l’affichage de ces boutons et fournir les vôtres. Cet article vous montre comment procéder.

## <a name="custom-video-positioning"></a>[Positionnement vidéo personnalisé](custom-positioning.md)

Chaque lecteur vidéo de plateforme a une barre de position qui montre la progression de la vidéo et vous permet de passer rapidement à une position en particulier, en avant ou en arrière. Cet article montre comment remplacer cette barre de position par un contrôle personnalisé.

## <a name="related-links"></a>Liens connexes

- [Démonstrations de lecteur vidéo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
