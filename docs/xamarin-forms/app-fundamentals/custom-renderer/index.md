---
title: Convertisseurs personnalisés Xamarin.Forms
description: Les convertisseurs personnalisés permettent aux développeurs de remplacer le rendu par défaut des contrôles natifs de chaque plateforme afin de personnaliser l’apparence et le comportement des contrôles Xamarin.Forms.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2019
ms.openlocfilehash: b87e713f89951d03408fa559bcf6e02cdae65e28
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "74824244"
---
# <a name="xamarinforms-custom-renderers"></a>Convertisseurs personnalisés Xamarin.Forms

_Les interfaces utilisateur Xamarin.Forms sont rendues à l’aide des commandes natives de la plate-forme cible, ce qui permet aux applications Xamarin.Forms de conserver l’apparence et la sensation appropriées pour chaque plate-forme. Les rendus personnalisés permettent aux développeurs de remplacer ce processus pour personnaliser l’apparence et le comportement des contrôles Xamarin.Forms sur chaque plate-forme._

## <a name="introduction-to-custom-renderers"></a>[Présentation des convertisseurs personnalisés](introduction.md)

Les convertisseurs personnalisés fournissent une approche puissante pour personnaliser l’apparence et le comportement des contrôles Xamarin.Forms. Ils peuvent être utilisés aussi bien pour les petits changements de style que pour la personnalisation de la disposition sophistiquée d’une plateforme et du comportement. Cet article présente les convertisseurs personnalisés et décrit leur processus de création.

## <a name="renderer-base-classes-and-native-controls"></a>[Classes de base de rendu et contrôles indigènes](renderers.md)

Chaque contrôle Xamarin.Forms est accompagné d’un convertisseur pour chaque plateforme qui crée une instance de contrôle natif. Cet article liste les classes de renderer et de contrôle natif qui implémentent chaque page, disposition, vue et cellule Xamarin.Forms.

## <a name="customizing-an-entry"></a>[Personnalisation d’une entrée](entry.md)

Le contrôle Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) permet de modifier une seule ligne de texte. Cet article montre comment créer un convertisseur personnalisé pour le contrôle `Entry` afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation propre à la plateforme.

## <a name="customizing-a-contentpage"></a>[Personnalisation d’un ContentPage](contentpage.md)

A [`ContentPage`](xref:Xamarin.Forms.ContentPage) est un élément visuel qui affiche une vue unique et occupe la plupart de l’écran. Cet article montre comment créer un convertisseur personnalisé pour la page `ContentPage` afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation propre à la plateforme.

## <a name="customizing-a-map"></a>[Personnalisation d’une carte](map/index.md)

Xamarin.Forms.Maps fournit une abstraction multiplateforme pour afficher des cartes qui utilisent les API de carte natives de chaque plateforme, dans le but de créer une expérience rapide et familière pour les utilisateurs. Cet article montre comment créer des convertisseurs personnalisés pour le contrôle `Map` afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation propre à la plateforme.

## <a name="customizing-a-listview"></a>[Personnalisation d’un ListView](listview.md)

A Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) est une vue qui affiche une collection de données comme une liste verticale. Cet article montre comment créer un renderer personnalisé qui encapsule des contrôles de liste spécifiques à la plateforme et des dispositions de cellule natives afin de mieux contrôler les performances des contrôles de liste natifs.

## <a name="customizing-a-viewcell"></a>[Personnalisation d’une ViewCell](viewcell.md)

Un Xamarin.Forms [`ViewCell`](xref:Xamarin.Forms.ViewCell) est une cellule qui [`ListView`](xref:Xamarin.Forms.ListView) [`TableView`](xref:Xamarin.Forms.TableView)peut être ajoutée à un ou , qui contient une vue définie par le développeur. Cet article montre comment créer un convertisseur personnalisé pour un `ViewCell` hébergé à l’intérieur d’un contrôle `ListView` Xamarin.Forms. Cela empêche les appels répétés des calculs de disposition Xamarin.Forms pendant le défilement de `ListView`.

## <a name="customizing-a-webview"></a>[Personnalisation d’une WebView](hybridwebview.md)

Un Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) est une vue qui affiche le contenu Web et HTML de votre application. Cet article explique comment créer un rendu `WebView` personnalisé qui étend le pour permettre le code C ' d’être invoqué à partir de JavaScript.

## <a name="implementing-a-view"></a>[Implémentation d’une vue](view.md)

Les contrôles des interfaces utilisateur personnalisées [`View`](xref:Xamarin.Forms.View) Xamarin.Forms doivent dériver de la classe, qui est utilisée pour placer des mises en page et des contrôles sur l’écran. Cet article montre comment créer un convertisseur personnalisé pour un contrôle personnalisé Xamarin.Forms qui est utilisé afin d’afficher un aperçu du flux vidéo à partir de l’appareil photo de l’appareil.

## <a name="implementing-a-video-player"></a>[Mise en œuvre d’un lecteur vidéo](video-player/index.md)

Cet article montre comment écrire des convertisseurs pour implémenter un contrôle `VideoPlayer` qui peut lire des vidéos à partir du web, des vidéos incorporées comme des ressources d’application ou des vidéos stockées dans la vidéothèque sur l’appareil de l’utilisateur. Plusieurs techniques sont présentées, y compris l’implémentation de méthodes et de propriétés en lecture seule pouvant être liées.
