---
ms.openlocfilehash: e383bbccd4e76be8a208f5680e5cf21e45a0dbc3
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511948"
---

La ligne de commande suivante pour spécifier une version Release de la solution **SOLUTION_FILE. sln** pour l’iPhone. L’emplacement de la loi peut être défini en spécifiant `IpaPackageDir` la propriété sur la ligne de commande:

- Sur le Mac, à l’aide de **xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

La commande **xbuild** se trouve généralement dans le répertoire **/Library/Frameworks/mono.Framework/Commands**.

- Sur Windows, à l’aide de **MSBuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**MSBuild** ne développe `$( )` pas automatiquement les expressions passées par la ligne de commande. Pour cette raison, il est recommandé d’utiliser un chemin d’accès complet `IpaPackageDir` lorsque vous définissez le au niveau de la ligne de commande.

Pour plus d’informations sur la `IpaPackageDir` propriété, consultez les [notes de publication pour iOS 9,8](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location) .
