---
title: 'Erreur IBTool : impossible de terminer l’opération.'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 4ff7b88ad63870246acbf2b29d01775f6711a8ce
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031189"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Erreur IBTool : impossible de terminer l’opération.

## <a name="fixed-in-xcode-611"></a>Résolu dans Xcode 6.1.1

Apple a [corrigé](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) ce bogue `ibtool` dans Xcode 6.1.1. par conséquent, la mise à niveau vers Xcode 6.1.1 ou une version ultérieure est la solution la plus simple.

* * *

## <a name="description-of-the-problem"></a>Description du problème

La commande `ibtool` dans Xcode 6,0 a un bogue sur OS X 10,10 Yosemite. Xamarin. iOS utilise `ibtool` de Xcode pour compiler les storyboards et les fichiers de `XIB`.

Vous trouverez plus d’informations sur le bogue par rapport à Xcode sur les Stack Overflow suivantes : [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Message d'erreur

> Impossible d’ouvrir le document « fichier mainstoryboard. Storyboard ». Impossible d’effectuer l’opération. (erreur com. Apple. InterfaceBuilder-1.)

## <a name="workarounds-for-xcode-60"></a>Solutions de contournement (pour XCode 6,0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Option 1 : gérer tous les `UIImageView.Image` les propriétés dans le code

Au lieu de définir la propriété `Image` d’un `UIImageView` dans le fichier Storyboard ou `.xib`, vous pouvez définir la propriété dans l’une des méthodes override du cycle de vie View dans le contrôleur d’affichage (par exemple, dans `ViewDidLoad()`). Pour obtenir des conseils sur l’utilisation de `UIImage.FromBundle()` et `UIImage.FromFile()`, consultez également [utilisation des images](~/ios/app-fundamentals/images-icons/index.md) .

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Option 2 : déplacer toutes les ressources d’image vers le dossier de `Resources` de niveau supérieur

Après avoir déplacé les images vers le dossier de `Resources` de niveau supérieur, vous devez mettre à jour les fichiers de la table de montage séquentiel et `.xib` pour utiliser les nouveaux chemins d’images.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Option 3 : définir la `LogicalName` pour toutes les ressources d’image problématiques afin qu’elles soient copiées au niveau supérieur du bundle`.app`

Par exemple, imaginons que votre fichier d' `.csproj` d’origine contient l’entrée suivante :

`<BundleResource Include="Resources\Images\image.png" />`

Vous pouvez modifier cet élément et ajouter un `LogicalName` afin que l’image soit copiée au niveau supérieur du bundle `.app` :

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

Dans Visual Studio pour Mac la `LogicalName` peut également être définie à l’aide du champ `Resource ID` de l’image sous **affichage > propriétés**du bloc de >. (Voir aussi : [https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Après cette modification, vous devrez mettre à jour les fichiers de plan conceptuel et de `.xib` pour utiliser les nouveaux chemins d’images de niveau supérieur. Visual Studio pour Mac met automatiquement à jour la liste des saisies semi-automatiques pour la propriété `Image` dans le concepteur iOS. Dans Visual Studio, vous devez modifier le chemin d’accès manuellement. Le concepteur iOS affiche ensuite ce en tant qu’image manquante, mais le projet est généré et exécuté correctement.

### <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide, pour nous contacter ou, si le problème persiste même après l’utilisation des informations ci-dessus, consultez [les options de support disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, les suggestions et la façon de signaler un nouveau bogue si nécessaire. . 
