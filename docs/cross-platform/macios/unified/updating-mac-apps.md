---
title: Mise à jour des applications Mac existantes
description: Ce document décrit les étapes à suivre pour mettre à jour une application Xamarin. Mac à partir du API classique vers le API unifiée.
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: dc23aed074360a06fcef6ad540d91aa98a19318b
ms.sourcegitcommit: 9912e57ff6124c583600f9460ebfa3f7f7525960
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69560293"
---
# <a name="updating-existing-mac-apps"></a>Mise à jour des applications Mac existantes

La mise à jour d’une application existante pour utiliser le API unifiée nécessite des modifications du fichier projet proprement dit, ainsi que des espaces de noms et des API utilisés dans le code de l’application.

## <a name="the-road-to-64-bits"></a>La route à 64 bits

Les nouvelles API unifiées sont requises pour prendre en charge les architectures de périphérique 64 bits à partir d’une application Xamarin. Mac. Depuis le 1er février, 2015 Apple exige que toutes les nouvelles soumissions d’application à Mac App Store prennent en charge les architectures 64 bits.

Xamarin fournit des outils pour Visual Studio pour Mac et Visual Studio pour automatiser le processus de migration du API classique vers le API unifiée ou vous pouvez convertir les fichiers projet manuellement. Bien que l’utilisation des outils automatiques soit fortement suggérée, cet article aborde ces deux méthodes.

### <a name="before-you-start"></a>Avant de commencer...

Avant de mettre à jour votre code existant vers le API unifiée, il est fortement recommandé d’éliminer tous les *avertissements de compilation*. De nombreux *avertissements* dans le API classique deviendront des erreurs une fois que vous aurez migré vers Unified. Il est plus facile de les résoudre avant de commencer, car les messages du compilateur du API classique fournissent souvent des indications sur les éléments à mettre à jour.

## <a name="automated-updating"></a>Mise à jour automatisée

Une fois les avertissements résolus, sélectionnez un projet Mac existant dans Visual Studio pour Mac ou Visual Studio, puis choisissez **migrer vers Xamarin. Mac API unifiée** à partir du menu **projet** . Par exemple :

![](updating-mac-apps-images/beta-tool1.png "Choisissez migrer vers Xamarin. Mac API unifiée à partir du menu projet")

Vous devez accepter cet avertissement avant l’exécution de la migration automatisée (évidemment, vous devez vous assurer que vous disposez de sauvegardes/contrôle de code source avant de lancer cette aventure):

![](updating-mac-apps-images/migrate01.png "Accepter cet avertissement avant l’exécution de la migration automatisée")

Il existe deux types de Framework cible pris en charge qui peuvent être sélectionnés lors de l’utilisation de l’API unifiée dans une application Xamarin. Mac:

- **Framework mobile Xamarin. Mac** : il s’agit du même .NET Framework optimisé utilisé par Xamarin. iOS et Xamarin. Android prenant en charge un sous-ensemble de l’infrastructure de **Bureau** complète. Il s’agit de l’infrastructure recommandée, car elle fournit des binaires moyens plus petits en raison d’un comportement de liaison supérieur.
- **Xamarin. Mac .net 4,5 Framework** : ce Framework est à nouveau un sous-ensemble de l’infrastructure de **Bureau** . Toutefois, il supprime beaucoup moins de l’infrastructure de **Bureau** complète que l’infrastructure **mobile** et doit _«fonctionner» simplement_ avec la plupart des packages NuGet ou des bibliothèques tierces. Cela permet au développeur de consommer des assemblys de **Bureau** standard tout en continuant à utiliser un Framework pris en charge, mais cette option produit des lots d’applications plus importants. Il s’agit de l’infrastructure recommandée dans laquelle les assemblys .NET tiers utilisés ne sont pas compatibles avec l' **infrastructure mobile Xamarin. Mac**. Pour obtenir la liste des assemblys pris en charge, consultez notre documentation sur les [assemblys](~/cross-platform/internals/available-assemblies.md) .

Pour plus d’informations sur les frameworks cibles et les implications de la sélection d’une cible spécifique pour votre application Xamarin. Mac, consultez notre documentation sur les [frameworks cibles](~/mac/platform/target-framework.md) . 

L’outil automatise toutes les étapes décrites dans la section **mise à jour manuelle** présentée ci-dessous. il s’agit de la méthode suggérée pour convertir un projet Xamarin. Mac existant en API unifiée.

## <a name="steps-to-update-manually"></a>Étapes de mise à jour manuelle

Là encore, une fois les avertissements résolus, procédez comme suit pour mettre à jour manuellement les applications Xamarin. Mac afin d’utiliser le nouveau API unifiée:

### <a name="1-update-project-type--build-target"></a>1. Mettre à jour le type de projet & la cible de build

Remplacez la version de projet dans vos fichiers csproj `42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` par `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`. Modifiez le fichier **csproj** dans un éditeur de texte en remplaçant le premier élément de `<ProjectTypeGuids>` l’élément comme indiqué ci-dessous:

![](updating-mac-apps-images/csproj.png "Modifiez le fichier csproj dans un éditeur de texte en remplaçant le premier élément de l’élément ProjectTypeGuids comme indiqué")

Modifiez l’élément **Import** qui contient `Xamarin.Mac.targets` `Xamarin.Mac.CSharp.targets` comme indiqué ci-dessous:

![](updating-mac-apps-images/csproj2.png "Remplacez l’élément import qui contient Xamarin. Mac. targets par Xamarin. Mac. CSharp. targets comme indiqué")

Ajoutez les lignes de code suivantes après l' `<AssemblyName>` élément:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

Exemple :

![Ajoutez ces lignes de code après l' \<élément AssemblyName >](updating-mac-apps-images/csproj3.png)

### <a name="2-update-project-references"></a>2. Mettre à jour les références de projet

Développez le nœud **références** du projet d’application Mac. Elle affiche initialement une référence * **XamMac** rompue semblable à cette capture d’écran (car nous venons de modifier le type de projet):

![](updating-mac-apps-images/references.png "Il affiche initialement une référence XamMac rompue similaire à cette capture d’écran.")

Cliquez sur l' **icône d’engrenage** en regard de l’entrée **XamMac** , puis sélectionnez **supprimer** pour supprimer la référence incorrecte.

Ensuite, cliquez avec le bouton droit sur le dossier **références** dans le **Explorateur de solutions** , puis sélectionnez **modifier les références**. Faites défiler vers le bas de la liste des références et cochez la case en regard de **Xamarin. Mac**.

![](updating-mac-apps-images/references2.png "Faites défiler vers le bas de la liste des références et cochez la case en regard de Xamarin. Mac")

Appuyez sur **OK** pour enregistrer les modifications apportées aux références du projet.

### <a name="3-remove-monomac-from-namespaces"></a>3. Supprimer MonoMac des espaces de noms

Supprimez le préfixe **MonoMac** des espaces `using` de noms dans les instructions ou partout où un className a été qualifié complet (par exemple, `MonoMac.AppKit`devient juste `AppKit`).

### <a name="4-remap-types"></a>4. Remapper les types

Des [types natifs](~/cross-platform/macios/nativetypes.md) ont été introduits, qui remplacent certains types précédemment utilisés, tels `System.Drawing.RectangleF` que `CoreGraphics.CGRect` les instances de avec (par exemple). La liste complète des types se trouve dans la page [types natifs](~/cross-platform/macios/nativetypes.md) .

### <a name="5-fix-method-overrides"></a>5. Remplacements de la méthode Fix

La `AppKit` signature de certaines méthodes a été modifiée pour utiliser les nouveaux [types natifs](~/cross-platform/macios/nativetypes.md) ( `nint`tels que). Si les sous-classes personnalisées remplacent ces méthodes, les signatures ne correspondent plus et génèrent des erreurs. Corrigez ces substitutions de méthode en modifiant la sous-classe pour qu’elle corresponde à la nouvelle signature à l’aide des types natifs. 

## <a name="considerations"></a>Considérations

Les considérations suivantes doivent être prises en compte lors de la conversion d’un projet Xamarin. Mac existant du API classique au nouveau API unifiée si cette application s’appuie sur un ou plusieurs composants ou un package NuGet. 

### <a name="components"></a>Composants

Tout composant que vous avez inclus dans votre application doit également être mis à jour vers la API unifiée ou un conflit se produira lorsque vous essaierez de compiler. Pour tout composant inclus, remplacez la version actuelle par une nouvelle version du magasin de composants Xamarin qui prend en charge le API unifiée et effectuez une génération propre. Tout composant qui n’a pas encore été converti par l’auteur affiche un avertissement 32 bits uniquement dans le magasin de composants.

### <a name="nuget-support"></a>Prise en charge par NuGet

Bien que nous ayons apporté des modifications à NuGet pour qu’il fonctionne avec la prise en charge API unifiée, il n’y a pas eu de nouvelle version de NuGet. nous allons donc évaluer comment obtenir NuGet pour reconnaître les nouvelles API. 

Jusqu’à ce moment-là, tout comme les composants, vous devrez basculer tout package NuGet inclus dans votre projet vers une version qui prend en charge les API unifiées et effectuer une génération propre par la suite.

> [!IMPORTANT]
> Si vous avez une erreur au format _«l’erreur 3 ne peut pas inclure à la fois «monomac. dll» et «Xamarin. Mac. dll» dans le même projet Xamarin. Mac-«Xamarin. Mac. dll» est référencée explicitement, tandis que «monomac. dll» est référencé par «xxx, version = 0.0.000, culture = neutral, PublicKeyToken = null «»_ après la conversion de votre application en API unifiées, il est généralement nécessaire de disposer d’un composant ou d’un package NuGet dans le projet qui n’a pas été mis à jour vers l’API unifiée. Vous devez supprimer le composant/NuGet existant, effectuer une mise à jour vers une version qui prend en charge les API unifiées et effectuer une génération propre.

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Activation des versions 64 bits des applications Xamarin. Mac

Pour une application mobile Xamarin. Mac qui a été convertie en API unifiée, le développeur doit toujours activer la génération de l’application pour les ordinateurs 64 bits à partir des options de l’application. Pour obtenir des instructions détaillées sur l’activation des builds bits 64, consultez le document **activation des builds 64 bits des applications Xamarin. Mac** du document [sur la plateforme 32/64 bits](~/cross-platform/macios/32-and-64/index.md) .
    
## <a name="finishing-up"></a>Finalisation

Que vous choisissiez ou non d’utiliser la méthode automatique ou manuelle pour convertir votre application Xamarin. Mac du modèle Classic en API unifiées, il existe plusieurs instances qui nécessitent une intervention manuelle supplémentaire. Consultez nos [conseils pour mettre à jour le code dans le document API unifiée](~/cross-platform/macios/unified/updating-tips.md) pour les problèmes connus et les contournements.

## <a name="related-links"></a>Liens associés

- [Conseils pour la mise à jour du code vers l’API unifiée](~/cross-platform/macios/unified/updating-tips.md)
- [Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/native-types-cross-platform.md)
- [Différences entre les API unifiée classiques et les différences](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
