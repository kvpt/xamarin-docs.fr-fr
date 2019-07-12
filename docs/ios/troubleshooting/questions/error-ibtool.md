---
title: 'Erreur IBTool : Impossible d’effectuer l’opération.'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: b894c245a4391064746a08816dfa63a1148d436d
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831249"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Erreur IBTool : Impossible d’effectuer l’opération.

## <a name="fixed-in-xcode-611"></a>Fixe dans Xcode 6.1.1

Apple [fixe](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) cela `ibtool` bogue dans Xcode 6.1.1, par conséquent, la mise à niveau à Xcode 6.1.1 ou une version ultérieure est le plus simple.

* * *

## <a name="description-of-the-problem"></a>Description du problème

Le `ibtool` commande dans Xcode 6.0 a rencontré un bogue sur OS X 10.10 Yosemite. Xamarin.iOS utilise de Xcode `ibtool` pour compiler des storyboards et `XIB` fichiers.

Vous trouverez plus d’informations sur le bogue par rapport à Xcode sur ce qui suit post Stack Overflow : [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Message d’erreur

> Le document « MainStoryboard.storyboard » n’a pas pu être ouvert. Impossible d’effectuer l’opération. (erreur com.apple.InterfaceBuilder -1.)

## <a name="workarounds-for-xcode-60"></a>Solutions de contournement (Xcode 6.0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Option 1 : Gérer tous les `UIImageView.Image` propriétés dans le code

Plutôt que de définir la `Image` propriété d’un `UIImageView` dans la table de montage séquentiel ou `.xib` fichier, vous pouvez définir la propriété dans un de la vue de cycle de vie des méthodes de remplacement dans le contrôleur d’affichage (par exemple, dans `ViewDidLoad()`). Voir aussi [utilisation des Images](~/ios/app-fundamentals/images-icons/index.md) pour obtenir des conseils sur l’utilisation de `UIImage.FromBundle()` et `UIImage.FromFile()`.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Option 2 : Déplacer toutes les ressources de l’image vers le niveau supérieur `Resources` dossier

Après avoir déplacé les images vers le niveau supérieur `Resources` dossier, vous devez mettre à jour la table de montage séquentiel et `.xib` fichiers à utiliser les nouveaux chemins d’image.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Option 3 : Définir le `LogicalName` pour les ressources d’image problématique afin qu’ils sont copiés vers le niveau supérieur de la`.app` bundle

Par exemple, votre original `.csproj` fichier contient l’entrée suivante :

`<BundleResource Include="Resources\Images\image.png" />`

Vous pouvez modifier cet élément et ajouter un `LogicalName` afin que l’image est copié à la place pour le niveau supérieur de la `.app` bundle :

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

Dans Visual Studio pour Mac le `LogicalName` peut également être définie à l’aide de la `Resource ID` champ pour l’image sous **Afficher > panneaux > Propriétés**. (Voir aussi : [ https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545 ](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Après cette modification, vous devez mettre à jour la table de montage séquentiel et `.xib` fichiers à utiliser les nouveaux chemins d’image de niveau supérieur. Visual Studio pour Mac met met automatiquement à jour la liste des saisies semi-automatiques pour le `Image` propriété dans le concepteur iOS. Dans Visual Studio, vous devez modifier le chemin d’accès à la main. Le concepteur iOS affichera alors cela comme une image manquante, mais le projet va générer et exécuter correctement.

### <a name="next-steps"></a>Étapes suivantes

Pour obtenir une assistance supplémentaire, pour nous contacter, ou si le reste de ce problème même après utilisant les informations ci-dessus, veuillez consulter [quelles options de prise en charge sont disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, des suggestions, ainsi que comment signaler un bogue nouveau si nécessaire. 

