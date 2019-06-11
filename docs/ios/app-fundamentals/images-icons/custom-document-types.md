---
title: Icônes de Document personnalisées dans Xamarin.iOS
description: Cet article explique comment, y compris et la gestion d’une ressource image dans une application Xamarin.iOS à être utilisé comme une icône de Type de Document personnalisé.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/23/2017
ms.openlocfilehash: 7520d3984a204dbceb67ef5310a10b1571f03127
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827385"
---
# <a name="custom-document-icons-in-xamarinios"></a>Icônes de Document personnalisées dans Xamarin.iOS

_Cet article explique comment, y compris et la gestion d’une ressource image dans une application Xamarin.iOS à être utilisé comme une icône de Type de Document personnalisé._

Si une application Xamarin.iOS prend en charge le chargement d’un type de document particulier, le développeur peut fournir des icônes que le système utilisera quand il rencontre ce type de document, par exemple quand un utilisateur appuie sur une pièce jointe dans le *Application Mail* en tant que indiqué ici :

 [![](custom-document-types-images/17.png "Un exemple d’icônes de type de document")](custom-document-types-images/17.png#lightbox)

Le développeur peut ajouter des informations de type de document pour un format de fichier l’application est capable d’ouvrir en incluant des entrées de dictionnaire pour la `CFBundleTypeName` chaîne et `LSItemContentTypes` tableau dans l’application `Info.plist`. Les icônes pour le type de document aller dans le `CFBundleTypeIconFiles` tableau. Si une icône de document n’est pas fourni, un à partir de l’icône d’application dérivera iOS.
Icônes peuvent être fournies pour différentes tailles, optimisés pour les différentes résolutions d’appareil. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour affecter ces valeurs dans Visual Studio pour Mac, utilisez le **Types de documents** section sous le **avancé** onglet sur le `Info.plist` éditeur pour ajouter le type de document et de lui assigner des icônes d’image. Par exemple, voici une capture d’écran montrant l’enregistrement pour la prise en charge PDF :

 [![](custom-document-types-images/18.png "La section Types de documents sous l’onglet Avancé de l’éditeur 'Info.plist'")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour affecter ces valeurs dans Visual Studio, utilisez le **Types de documents** section sous le **avancé** onglet sur le `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Ouvrez la section Types de documents sous l’onglet Avancé")

Cliquez sur le **ajouter un Type de Document** bouton et renseignez les champs obligatoires :

![](custom-document-types-images/doc02w.png "Le formulaire Ajouter un Type de Document")

-----


Pour plus d’informations sur les types de documents, consultez d’Apple [uniforme référence d’identificateurs de Type](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) et [rubriques de programmation Interaction de Document pour iOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).


## <a name="related-links"></a>Liens associés

- [Utilisation d’Images (exemple)](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icône personnalisée et les instructions de création d’Image](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
