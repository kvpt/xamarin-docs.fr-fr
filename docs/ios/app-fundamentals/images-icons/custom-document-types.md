---
title: Icônes de document personnalisées dans Xamarin. iOS
description: Cet article traite de l’ajout et de la gestion d’une ressource d’image dans une application Xamarin. iOS à utiliser comme icône de type de document personnalisé.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/23/2017
ms.openlocfilehash: ac8ee96d6183f9a62233d217c75b03da15605bd2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004224"
---
# <a name="custom-document-icons-in-xamarinios"></a>Icônes de document personnalisées dans Xamarin. iOS

_Cet article traite de l’ajout et de la gestion d’une ressource d’image dans une application Xamarin. iOS à utiliser comme icône de type de document personnalisé._

Si une application Xamarin. iOS prend en charge le chargement d’un type de document particulier, le développeur peut fournir des icônes que le système utilisera lorsqu’il rencontre ce type de document, par exemple lorsqu’un utilisateur maintient une pièce jointe dans l' *application de messagerie* comme indiqué ici :

 [![](custom-document-types-images/17.png "An example of document type icons")](custom-document-types-images/17.png#lightbox)

Le développeur peut ajouter des informations de type de document pour un format de fichier que l’application peut ouvrir en incluant des entrées de dictionnaire pour la chaîne de `CFBundleTypeName` et `LSItemContentTypes` tableau dans le `Info.plist`de l’application. Les icônes du type de document sont placées dans le tableau `CFBundleTypeIconFiles`. Si aucune icône de document n’est fournie, iOS en dérivera un à partir de l’icône de l’application.
Les icônes peuvent être fournies pour plusieurs tailles, optimisées pour les différentes résolutions d’appareils. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour affecter ces valeurs dans Visual Studio pour Mac, utilisez la section **types de documents** sous l’onglet **avancé** de l’éditeur de `Info.plist` pour ajouter le type de document et lui affecter des icônes d’image. Par exemple, voici une capture d’écran montrant l’inscription de la prise en charge PDF :

 [![](custom-document-types-images/18.png "The Document Types section under the Advanced tab on the `Info.plist` editor")](custom-document-types-images/18.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour affecter ces valeurs dans Visual Studio, utilisez la section **types de documents** sous l’onglet **avancé** de la `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Open the Document Types section under the Advanced tab")

Cliquez sur le bouton **Ajouter un type de document** et renseignez les champs obligatoires :

![](custom-document-types-images/doc02w.png "The Add Document Type form")

-----

Pour plus d’informations sur les types de documents, consultez les rubriques de référence sur les [identificateurs de types uniformes](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) d’Apple et sur la [programmation des interactions de documents pour iOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).

## <a name="related-links"></a>Liens associés

- [Utilisation des images (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Instructions personnalisées de création d’images et d’icônes](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
