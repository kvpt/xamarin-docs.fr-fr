---
title: Xamarin.FormsConvertisseurs personnalisés
description: Les convertisseurs personnalisés permettent aux développeurs de substituer le rendu des contrôles natifs sur chaque plateforme, afin de personnaliser l’apparence et le comportement des Xamarin.Forms contrôles.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be281f9b7987a8d23ba6ac93f0771e432f277d45
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138916"
---
# <a name="xamarinforms-custom-renderers"></a>Xamarin.FormsConvertisseurs personnalisés

_Les interfaces utilisateur Xamarin. Forms sont rendues à l’aide des contrôles natifs de la plateforme cible, ce qui permet Xamarin.Forms aux applications de conserver l’apparence et la convivialité appropriées pour chaque plateforme. Les convertisseurs personnalisés permettent aux développeurs de substituer ce processus afin de personnaliser l’apparence et le comportement des Xamarin.Forms contrôles sur chaque plateforme._

## <a name="introduction-to-custom-renderers"></a>[Présentation des convertisseurs personnalisés](introduction.md)

Les convertisseurs personnalisés fournissent une approche puissante pour personnaliser l’apparence et le comportement des Xamarin.Forms contrôles. Ils peuvent être utilisés aussi bien pour les petits changements de style que pour la personnalisation de la disposition sophistiquée d’une plateforme et du comportement. Cet article présente les convertisseurs personnalisés et décrit leur processus de création.

## <a name="renderer-base-classes-and-native-controls"></a>[Classes de base de convertisseur et contrôles natifs](renderers.md)

Chaque Xamarin.Forms contrôle a un convertisseur associé pour chaque plateforme qui crée une instance d’un contrôle natif. Cet article répertorie les classes de convertisseur et de contrôle natif qui implémentent chaque Xamarin.Forms page, disposition, vue et cellule.

## <a name="customizing-an-entry"></a>[Personnalisation d’une entrée](entry.md)

Le Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) contrôle autorise la modification d’une seule ligne de texte. Cet article montre comment créer un convertisseur personnalisé pour le contrôle `Entry` afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation propre à la plateforme.

## <a name="customizing-a-contentpage"></a>[Personnalisation d’un ContentPage](contentpage.md)

Un [`ContentPage`](xref:Xamarin.Forms.ContentPage) est un élément visuel qui affiche une vue unique et occupe la majeure partie de l’écran. Cet article montre comment créer un convertisseur personnalisé pour la page `ContentPage` afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation propre à la plateforme.

## <a name="customizing-a-map-pin"></a>[Personnalisation d’une épingle de carte](map-pin.md)

Xamarin.Forms. Maps fournit une abstraction multiplateforme pour afficher les mappages qui utilisent les API Map natives sur chaque plateforme, afin de fournir une expérience cartographique rapide et familière aux utilisateurs. Cette rubrique montre comment créer un convertisseur personnalisé pour le `Map` contrôle, ce qui permet aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation propre à la plateforme.

## <a name="customizing-a-listview"></a>[Personnalisation d’un ListView](listview.md)

Un Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) est une vue qui affiche une collection de données sous la forme d’une liste verticale. Cet article montre comment créer un renderer personnalisé qui encapsule des contrôles de liste spécifiques à la plateforme et des dispositions de cellule natives afin de mieux contrôler les performances des contrôles de liste natifs.

## <a name="customizing-a-viewcell"></a>[Personnalisation d’une ViewCell](viewcell.md)

Un Xamarin.Forms [`ViewCell`](xref:Xamarin.Forms.ViewCell) est une cellule qui peut être ajoutée à un [`ListView`](xref:Xamarin.Forms.ListView) ou [`TableView`](xref:Xamarin.Forms.TableView) , qui contient une vue définie par le développeur. Cet article montre comment créer un convertisseur personnalisé pour un `ViewCell` qui est hébergé à l’intérieur d’un Xamarin.Forms `ListView` contrôle. Cela empêche les Xamarin.Forms calculs de disposition d’être appelés de façon répétée pendant le `ListView` défilement.

## <a name="customizing-a-webview"></a>[Personnalisation d’une WebView](hybridwebview.md)

Un Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) est une vue qui affiche du contenu Web et HTML dans votre application. Cet article explique comment créer un convertisseur personnalisé qui étend le `WebView` pour permettre au code C# d’être appelé à partir de JavaScript.

## <a name="implementing-a-view"></a>[Implémentation d’une vue](view.md)

Xamarin.Formsles contrôles d’interface utilisateur personnalisés doivent dériver de la [`View`](xref:Xamarin.Forms.View) classe, qui est utilisée pour placer des dispositions et des contrôles à l’écran. Cet article explique comment créer un convertisseur personnalisé pour un Xamarin.Forms contrôle personnalisé utilisé pour afficher un flux vidéo de préversion à partir de l’appareil photo de l’appareil.

## <a name="implementing-a-video-player"></a>[Implémentation d’un lecteur vidéo](video-player/index.md)

Cet article montre comment écrire des convertisseurs pour implémenter un contrôle `VideoPlayer` qui peut lire des vidéos à partir du web, des vidéos incorporées comme des ressources d’application ou des vidéos stockées dans la vidéothèque sur l’appareil de l’utilisateur. Plusieurs techniques sont présentées, y compris l’implémentation de méthodes et de propriétés en lecture seule pouvant être liées.
