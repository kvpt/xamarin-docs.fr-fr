---
title: Icônes de document personnalisées dans Xamarin. iOS
description: Cet article traite de l’ajout et de la gestion d’une ressource d’image dans une application Xamarin. iOS à utiliser comme icône de type de document personnalisé.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/23/2017
ms.openlocfilehash: 3a74084db461271ca7fd440ab2c9779f949b30ff
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436844"
---
# <a name="custom-document-icons-in-xamarinios"></a>Icônes de document personnalisées dans Xamarin. iOS

_Cet article traite de l’ajout et de la gestion d’une ressource d’image dans une application Xamarin. iOS à utiliser comme icône de type de document personnalisé._

Si une application Xamarin. iOS prend en charge le chargement d’un type de document particulier, le développeur peut fournir des icônes que le système utilisera lorsqu’il rencontre ce type de document, par exemple lorsqu’un utilisateur maintient une pièce jointe dans l' *application de messagerie* comme indiqué ici :

 [![Exemple d’icônes de type de document](custom-document-types-images/17.png)](custom-document-types-images/17.png#lightbox)

Le développeur peut ajouter des informations de type de document pour un format de fichier que l’application peut ouvrir en incluant des entrées de dictionnaire pour la `CFBundleTypeName` chaîne et le `LSItemContentTypes` tableau dans le de l’application `Info.plist` . Les icônes du type de document sont placées dans le `CFBundleTypeIconFiles` tableau. Si aucune icône de document n’est fournie, iOS en dérivera un à partir de l’icône de l’application.
Les icônes peuvent être fournies pour plusieurs tailles, optimisées pour les différentes résolutions d’appareils. 

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Pour affecter ces valeurs dans Visual Studio pour Mac, utilisez la section **types de documents** sous l’onglet **avancé** de l' `Info.plist` éditeur pour ajouter le type de document et lui affecter des icônes d’image. Par exemple, voici une capture d’écran montrant l’inscription de la prise en charge PDF :

 [![La section types de documents sous l’onglet avancé de l’éditeur’info. plist'](custom-document-types-images/18.png)](custom-document-types-images/18.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour affecter ces valeurs dans Visual Studio, utilisez la section **types de documents** sous l’onglet **avancé** de la `Info.plist` :

 ![Ouvrir la section types de documents sous l’onglet avancé](custom-document-types-images/doc01w.png)

Cliquez sur le bouton **Ajouter un type de document** et renseignez les champs obligatoires :

![Formulaire ajouter un type de document](custom-document-types-images/doc02w.png)

-----

Pour plus d’informations sur les types de documents, consultez les rubriques de référence sur les [identificateurs de types uniformes](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) d’Apple et sur la [programmation des interactions de documents pour iOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).

## <a name="related-links"></a>Liens associés

- [Utilisation des images (exemple)](/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Instructions personnalisées de création d’images et d’icônes](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)