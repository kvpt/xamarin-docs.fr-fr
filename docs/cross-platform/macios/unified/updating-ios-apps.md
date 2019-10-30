---
title: Mise à jour des applications iOS existantes
description: Ce document décrit les étapes à suivre pour mettre à jour une application Xamarin. iOS à partir du API classique vers le API unifiée.
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 9b531bd095781c80c5f3418725d57f8f6bbb06fd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015032"
---
# <a name="updating-existing-ios-apps"></a>Mise à jour des applications iOS existantes

_Procédez comme suit pour mettre à jour une application Xamarin. iOS existante afin d’utiliser le API unifiée._

La mise à jour d’une application existante pour utiliser le API unifiée nécessite des modifications du fichier projet proprement dit, ainsi que des espaces de noms et des API utilisés dans le code de l’application.

## <a name="the-road-to-64-bits"></a>La route à 64 bits

Les nouvelles API unifiées sont requises pour prendre en charge les architectures de périphérique 64 bits à partir d’une application mobile Xamarin. iOS. Depuis le 1er février, 2015 Apple exige que toutes les nouvelles soumissions d’application à iTunes App Store prennent en charge les architectures 64 bits.

Xamarin fournit des outils pour Visual Studio pour Mac et Visual Studio pour automatiser le processus de migration du API classique vers le API unifiée ou vous pouvez convertir les fichiers projet manuellement. Bien que l’utilisation des outils automatiques soit fortement suggérée, cet article aborde ces deux méthodes.

### <a name="before-you-start"></a>Avant de commencer...

Avant de mettre à jour votre code existant vers le API unifiée, il est fortement recommandé d’éliminer tous les *avertissements de compilation*. De nombreux *avertissements* dans le API classique deviendront des erreurs une fois que vous aurez migré vers Unified. Il est plus facile de les résoudre avant de commencer, car les messages du compilateur du API classique fournissent souvent des indications sur les éléments à mettre à jour.

## <a name="automated-updating"></a>Mise à jour automatisée

Une fois les avertissements résolus, sélectionnez un projet iOS existant dans Visual Studio pour Mac ou Visual Studio, puis choisissez **migrer vers Xamarin. iOS API unifiée** à partir du menu **projet** . Exemple :

![](updating-ios-apps-images/beta-tool1.png "Choose Migrate to Xamarin.iOS Unified API from the Project menu")

Vous devez accepter cet avertissement avant l’exécution de la migration automatisée (évidemment, vous devez vous assurer que vous disposez de sauvegardes/contrôle de code source avant de lancer cette aventure) :

![](updating-ios-apps-images/beta-tool2.png "Agree to this warning before the automated migration will run")

L’outil automatise toutes les étapes décrites dans la section **mise à jour manuelle** présentée ci-dessous. il s’agit de la méthode suggérée pour convertir un projet Xamarin. iOS existant en API unifiée.

## <a name="steps-to-update-manually"></a>Étapes de mise à jour manuelle

Là encore, une fois les avertissements résolus, procédez comme suit pour mettre à jour manuellement les applications Xamarin. iOS afin d’utiliser le nouveau API unifiée :

### <a name="1-update-project-type--build-target"></a>1. mettre à jour le type de projet & cible de génération

Remplacez la version de projet dans vos fichiers **csproj** `6BC8ED88-2882-458C-8E55-DFD12B67127B` par `FEACFBD2-3405-455C-9665-78FE426C6842`. Modifiez le fichier **csproj** dans un éditeur de texte en remplaçant le premier élément de l’élément `<ProjectTypeGuids>` comme indiqué ci-dessous :

![](updating-ios-apps-images/csproj.png "Edit the csproj file in a text editor, replacing the first item in the ProjectTypeGuids element as shown")

Remplacez l’élément **Import** qui contient `Xamarin.MonoTouch.CSharp.targets` par `Xamarin.iOS.CSharp.targets` comme indiqué ci-dessous :

![](updating-ios-apps-images/csproj2.png "Change the Import element that contains Xamarin.MonoTouch.CSharp.targets to Xamarin.iOS.CSharp.targets as shown")

### <a name="2-update-project-references"></a>2. mettre à jour les références de projet

Développez le nœud **références** du projet d’application iOS. Il affiche initialement une référence * de type **monocontact** et cassée semblable à cette capture d’écran (car nous venons de modifier le type de projet) :

![](updating-ios-apps-images/references.png "It will initially show a broken- monotouch reference similar to this screenshot because the project type changed")

Cliquez avec le bouton droit sur le projet d’application iOS pour **modifier les références**, puis cliquez sur la référence **monotactile** et supprimez-la à l’aide du bouton rouge « X ».

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Right-click on the iOS application project to Edit References, then click on the monotouch reference and delete it using the red X button")

À présent, faites défiler jusqu’à la fin de la liste des références et cochez l’assembly **Xamarin. iOS** .

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Now scroll to the end of the references list and tick the Xamarin.iOS assembly")

Appuyez sur **OK** pour enregistrer les modifications apportées aux références du projet.

### <a name="3-remove-monotouch-from-namespaces"></a>3. supprimer les monotactiles des espaces de noms

Supprimez le préfixe **unitouch** des espaces de noms dans les instructions `using` ou partout où un className a été qualifié complet (par exemple, `MonoTouch.UIKit` devient simplement `UIKit`).

### <a name="4-remap-types"></a>4. remapper les types

Des [types natifs](~/cross-platform/macios/nativetypes.md) ont été introduits, qui remplacent certains types précédemment utilisés, tels que les instances de `System.Drawing.RectangleF` avec `CoreGraphics.CGRect` (par exemple). La liste complète des types se trouve dans la page [types natifs](~/cross-platform/macios/nativetypes.md) .

### <a name="5-fix-method-overrides"></a>5. corriger les remplacements de la méthode

Certaines méthodes `UIKit` ont été modifiées pour utiliser les nouveaux [types natifs](~/cross-platform/macios/nativetypes.md) (tels que `nint`). Si les sous-classes personnalisées remplacent ces méthodes, les signatures ne correspondent plus et génèrent des erreurs. Corrigez ces substitutions de méthode en modifiant la sous-classe pour qu’elle corresponde à la nouvelle signature à l’aide des types natifs.

Les exemples incluent la modification des `public override int NumberOfSections (UITableView tableView)` pour retourner des `nint` et la modification du type de retour et des types de paramètres dans `public override int RowsInSection (UITableView tableView, int section)` en `nint`.

## <a name="considerations"></a>Éléments à prendre en considération

Les considérations suivantes doivent être prises en compte lors de la conversion d’un projet Xamarin. iOS existant du API classique au nouveau API unifiée si cette application s’appuie sur un ou plusieurs composants ou un package NuGet.

### <a name="components"></a>Composants

Tout composant que vous avez inclus dans votre application doit également être mis à jour vers la API unifiée ou un conflit se produira lorsque vous essaierez de compiler. Pour tout composant inclus, remplacez la version actuelle par une nouvelle version du magasin de composants Xamarin qui prend en charge le API unifiée et effectuez une génération propre. Tout composant qui n’a pas encore été converti par l’auteur affiche un avertissement 32 bits uniquement dans le magasin de composants.

### <a name="nuget-support"></a>Prise en charge par NuGet

Bien que nous ayons apporté des modifications à NuGet pour qu’il fonctionne avec la prise en charge API unifiée, il n’y a pas eu de nouvelle version de NuGet. nous allons donc évaluer comment obtenir NuGet pour reconnaître les nouvelles API.

Jusqu’à ce moment-là, tout comme les composants, vous devrez basculer tout package NuGet inclus dans votre projet vers une version qui prend en charge les API unifiées et effectuer une génération propre par la suite.

> [!IMPORTANT]
> Si vous avez une erreur au format _« l’erreur 3 ne peut pas inclure à la fois «MonoTouch. dll » et « Xamarin. iOS. dll » dans le même projet Xamarin. iOS-« Xamarin. iOS. dll » est référencé explicitement, tandis que « unitouch. dll » est référencé par «xxx, version = 0.0.000, culture = neutral, PublicKeyToken = null'»_ après la conversion de votre application en API unifiées, il est généralement nécessaire de disposer d’un composant ou d’un package NuGet dans le projet qui n’a pas été mis à jour vers l’API unifiée. Vous devez supprimer le composant/NuGet existant, effectuer une mise à jour vers une version qui prend en charge les API unifiées et effectuer une génération propre.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Activation des versions 64 bits des applications Xamarin. iOS

Pour une application mobile Xamarin. iOS qui a été convertie en API unifiée, le développeur doit toujours activer la génération de l’application pour les ordinateurs 64 bits à partir des options de l’application. Pour obtenir des instructions détaillées sur l’activation des versions 64 bits, consultez le document **activation des builds 64 bits des applications Xamarin. iOS** du document [sur les plateformes 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) .

## <a name="finishing-up"></a>Finalisation

Que vous choisissiez ou non d’utiliser la méthode automatique ou manuelle pour convertir votre application Xamarin. iOS du modèle Classic en API unifiées, il existe plusieurs instances qui nécessitent une intervention manuelle supplémentaire. Consultez nos [conseils pour mettre à jour le code dans le document API unifiée](~/cross-platform/macios/unified/updating-tips.md) pour les problèmes connus et les contournements.

## <a name="related-links"></a>Liens associés

- [Conseils pour la mise à jour du code vers l’API unifiée](~/cross-platform/macios/unified/updating-tips.md)
- [Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/native-types-cross-platform.md)
- [Différences entre les API unifiée classiques et les différences](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
