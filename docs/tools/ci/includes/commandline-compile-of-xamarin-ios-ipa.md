---
ms.openlocfilehash: ef16224b9ef060ff8643211b8e1f0c767c518bdf
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841475"
---

La ligne de commande suivante pour spécifier une version Release de la solution **SOLUTION_FILE.sln** pour iPhone. L’emplacement de la loi peut être défini en spécifiant le `IpaPackageDir` propriété sur la ligne de commande :

- Sur le Mac, à l’aide de **xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

Le **xbuild** commande se trouve généralement dans le répertoire **/Library/Frameworks/Mono.framework/Commands**.

- Sur Windows, à l’aide de **msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**MSBuild** ne sont pas développées automatiquement `$( )` expressions passées par la ligne de commande. Pour cette raison, il est recommandé d’utiliser un chemin d’accès complet lors de la définition du `IpaPackageDir` en ligne de commande.


Consultez le [Notes de publication pour iOS 9.8](https://developer.xamarin.com/releases/ios/xamarin.ios_9/xamarin.ios_9.8/#New_MSBuild_property_IpaPackageDir_to_customize_.ipa_output_location) pour plus d’informations sur la `IpaPackageDir` propriété.
