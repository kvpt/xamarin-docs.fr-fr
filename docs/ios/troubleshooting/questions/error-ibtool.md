---
title: 'Erreur IBTool : Impossible d’effectuer l’opération.'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 39b522af5bdc3587e3d5aa1451ed4879c6af65f5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769335"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Erreur IBTool : Impossible d’effectuer l’opération.

## <a name="fixed-in-xcode-611"></a>Résolu dans Xcode 6.1.1

Apple a [corrigé](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) ce bogue `ibtool` dans Xcode 6.1.1. par conséquent, la mise à niveau vers Xcode 6.1.1 ou une version ultérieure est la solution la plus simple.

* * *

## <a name="description-of-the-problem"></a>Description du problème

La `ibtool` commande de Xcode 6,0 a un bogue sur OS X 10,10 Yosemite. Xamarin. iOS utilise Xcode `ibtool` pour compiler les fichiers et les `XIB` storyboards.

Vous trouverez plus d’informations sur le bogue par rapport à Xcode sur les Stack Overflow suivantes :[https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Message d’erreur

> Impossible d’ouvrir le document « fichier mainstoryboard. Storyboard ». Impossible d’effectuer l’opération. (erreur com. Apple. InterfaceBuilder-1.)

## <a name="workarounds-for-xcode-60"></a>Solutions de contournement (pour XCode 6,0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Option 1 : Gérer toutes `UIImageView.Image` les propriétés dans le code

Au lieu de définir `Image` la propriété d' `UIImageView` un dans le fichier `.xib` ou le Storyboard, vous pouvez définir la propriété dans l’une des méthodes de substitution du cycle de vie de vue dans le contrôleur d' `ViewDidLoad()`affichage (par exemple, dans). Consultez également [utilisation d’images](~/ios/app-fundamentals/images-icons/index.md) pour obtenir des conseils `UIImage.FromBundle()` sur l' `UIImage.FromFile()`utilisation de vs.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Option 2 : Déplacer toutes les ressources d’image vers le dossier de niveau `Resources` supérieur

Après avoir déplacé les images vers le dossier de `Resources` niveau supérieur, vous devez mettre à jour la table `.xib` de montage séquentiel et les fichiers pour utiliser les nouveaux chemins d’images.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Option 3 : Définir le `LogicalName` pour toutes les ressources d’image problématiques afin qu’elles soient copiées au`.app` niveau supérieur du bundle

Par exemple, imaginons que `.csproj` votre fichier d’origine contient l’entrée suivante :

`<BundleResource Include="Resources\Images\image.png" />`

Vous pouvez modifier cet élément et ajouter un `LogicalName` afin que l’image soit copiée au niveau supérieur `.app` du bundle :

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

Dans Visual Studio pour Mac `LogicalName` , peut également être défini à l' `Resource ID` aide du champ de l’image sous **Affichage > propriétés de pad >** . (Voir aussi : [https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Après cette modification, vous devrez mettre à jour la table de `.xib` montage séquentiel et les fichiers pour utiliser les nouveaux chemins d’accès aux images de niveau supérieur. Visual Studio pour Mac met automatiquement à jour la liste des saisies semi- `Image` automatiques pour la propriété dans le concepteur iOS. Dans Visual Studio, vous devez modifier le chemin d’accès manuellement. Le concepteur iOS affiche ensuite ce en tant qu’image manquante, mais le projet est généré et exécuté correctement.

### <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide, pour nous contacter ou, si le problème persiste même après l’utilisation des informations ci-dessus, consultez [les options de support disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, les suggestions et la façon de signaler un nouveau bogue si nécessaire. . 
