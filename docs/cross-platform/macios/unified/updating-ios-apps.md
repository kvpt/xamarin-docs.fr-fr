---
title: La mise à jour des applications iOS existantes
description: Ce document décrit les étapes qui doivent être remplies pour mettre à jour une application Xamarin.iOS à partir de l’API classique à l’API unifiée.
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4e471aaca2a7a5f247b21dd1c1863a01b062a716
ms.sourcegitcommit: afe9d93373d66eb45d82cabefca83b5733969634
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855754"
---
# <a name="updating-existing-ios-apps"></a>La mise à jour des applications iOS existantes

_Suivez ces étapes pour mettre à jour une application Xamarin.iOS existante pour utiliser l’API unifiée._

La mise à jour une application existante pour utiliser l’API unifiée nécessite des modifications au fichier projet lui-même ainsi que pour les espaces de noms et les API utilisées dans le code d’application.

## <a name="the-road-to-64-bits"></a>La route à 64 Bits

Les nouvelles API unifiée sont requis pour prendre en charge les architectures 64 bits depuis une application mobile Xamarin.iOS. À compter du 1er février 2015 Apple exige que tous les envois d’application nouveau vers le Store d’application iTunes prennent en charge les architectures 64 bits.

Xamarin fournit des outils pour Visual Studio pour Mac et Visual Studio automatiser le processus de migration à partir de l’API classique à l’API unifiée ou vous pouvez convertir les fichiers projet manuellement. Bien que les outils automatique à l’aide est fortement recommandé, cet article décrit les deux méthodes.

### <a name="before-you-start"></a>Avant de commencer...

Avant de mettre à jour de votre code existant vers l’API unifiée, il est vivement recommandé que vous supprimez toutes les *avertissements de compilation*. Nombreux *avertissements* dans l’API classique deviendra erreurs une fois que vous migrez vers unifiée. Il est plus facile de les corriger avant de commencer, car les messages du compilateur à partir de l’API classique fournissent souvent des indications sur les éléments à mettre à jour.

## <a name="automated-updating"></a>Mise à jour automatisée

Une fois que les avertissements ont été résolus, sélectionnez un projet iOS dans Visual Studio pour Mac ou Visual Studio et choisissez **migrer vers l’API unifiée Xamarin.iOS** à partir de la **projet** menu. Par exemple :

![](updating-ios-apps-images/beta-tool1.png "Choisissez de migrer vers API unifiée Xamarin.iOS dans le menu projet")

Vous devrez acceptez cet avertissement avant l’exécution de la migration automatisée (évidemment, vous devez vous assurer vous contrôlez sauvegardes/source avant de passer à cette aventure) :

![](updating-ios-apps-images/beta-tool2.png "Acceptez cet avertissement avant l’exécution de la migration automatisée")

L’outil automatise essentiellement toutes les étapes décrites dans le **mise à jour manuellement** section présentés ci-dessous et est la méthode conseillée de conversion d’un projet Xamarin.iOS existant vers l’API unifiée.

## <a name="steps-to-update-manually"></a>Étapes pour mettre à jour manuellement

Là encore, une fois que les avertissements ont été résolus, procédez comme suit pour mettre à jour manuellement les applications pour utiliser la nouvelle API unifiée Xamarin.iOS :

### <a name="1-update-project-type--build-target"></a>1. Type de projet de mise à jour & cible de génération

Modifier la version de projet dans votre **csproj** fichiers à partir de `6BC8ED88-2882-458C-8E55-DFD12B67127B` à `FEACFBD2-3405-455C-9665-78FE426C6842`. Modifier le **csproj** fichier dans un éditeur de texte, en remplaçant le premier élément dans le `<ProjectTypeGuids>` élément, comme indiqué :

![](updating-ios-apps-images/csproj.png "Modifiez le fichier csproj dans un éditeur de texte, en remplaçant le premier élément dans l’élément ProjectTypeGuids comme indiqué")

Modifier le **importation** élément contenant `Xamarin.MonoTouch.CSharp.targets` à `Xamarin.iOS.CSharp.targets` comme indiqué :

![](updating-ios-apps-images/csproj2.png "Modifier l’élément Import qui contient Xamarin.MonoTouch.CSharp.targets à Xamarin.iOS.CSharp.targets comme indiqué")

### <a name="2-update-project-references"></a>2. Mettre à jour les références de projet

Développez le projet application iOS **références** nœud. Elle affiche initialement un * interrompue - **monotouch** référence identique à cette capture d’écran (étant donné que nous venons de modifier le type de projet) :

![](updating-ios-apps-images/references.png "Il affichent initialement une référence rompue-monotouch similaire à cette capture d’écran, car le type de projet modifié")

Avec le bouton droit sur le projet d’application iOS à **modifier les références**, puis cliquez sur le **monotouch** référencer et supprimez-la à l’aide du bouton « X » rouge.

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Avec le bouton droit sur le projet d’application iOS pour modifier les références, puis cliquez sur la référence monotouch et supprimer à l’aide de la croix rouge bouton X")

Ensuite, faites défiler jusqu'à la fin de la liste des références et des graduations le **Xamarin.iOS** assembly.

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Ensuite, faites défiler jusqu'à la fin de la liste de références et de la graduation de l’assembly Xamarin.iOS")

Appuyez sur **OK** pour enregistrer des modifications de références de projet.

### <a name="3-remove-monotouch-from-namespaces"></a>3. Supprimer MonoTouch d’espaces de noms

Supprimer le **MonoTouch** préfixe à partir d’espaces de noms dans `using` instructions ou partout où un nom de classe a été entièrement qualifié (par ex. `MonoTouch.UIKit` devient simplement `UIKit`).

### <a name="4-remap-types"></a>4. Remapper les Types

[Les types natifs](~/cross-platform/macios/nativetypes.md) ont été introduites qui remplacent certains Types qui ont précédemment été utilisés, tels que les instances de `System.Drawing.RectangleF` avec `CoreGraphics.CGRect` (par exemple). Vous trouverez la liste complète des types sur le [types natifs](~/cross-platform/macios/nativetypes.md) page.

### <a name="5-fix-method-overrides"></a>5. Corriger les substitutions de méthode

Certains `UIKit` méthodes ont leur signature modifié pour utiliser le nouveau [types natifs](~/cross-platform/macios/nativetypes.md) (tel que `nint`). Si les sous-classes personnalisés substituer ces méthodes les signatures ne correspondront plus et produiront des erreurs. Corrigez ces substitutions de méthode en modifiant la sous-classe pour correspondre à la nouvelle signature à l’aide des types natifs.

Quelques exemples : remplacer `public override int NumberOfSections (UITableView tableView)` pour retourner `nint` et en modifiant les type de retour et les types de paramètre dans `public override int RowsInSection (UITableView tableView, int section)` à `nint`.

## <a name="considerations"></a>Considérations

Les considérations suivantes doivent être prises en compte lors de la conversion d’un projet Xamarin.iOS existant à partir de l’API classique à la nouvelle API unifiée si cette application s’appuie sur un ou plusieurs composants ou un NuGet Package.

### <a name="components"></a>Composants

N’importe quel composant que vous avez inclus dans votre application doit également être mis à jour vers l’API unifiée ou vous obtiendrez un conflit lorsque vous essayez de compiler. Pour les composants inclus, remplacez la version actuelle par une nouvelle version à partir du Store de composant Xamarin qui prend en charge de l’API unifiée et effectuer un nettoyage de build. Tout composant qui n’a pas encore été converti par l’auteur, affichera un 32 bits uniquement avertissement dans le magasin de composants.

### <a name="nuget-support"></a>Prise en charge par NuGet

Bien que nous contribué les modifications apportées à NuGet pour travailler avec la prise en charge de l’API unifiée, il n’a pas été une nouvelle version de NuGet, donc nous évaluons l’obtention de NuGet pour reconnaître les nouvelles API.

En attendant, tout comme les composants, vous devrez passer n’importe quel NuGet Package que vous avez inclus dans votre projet vers une version qui prend en charge les API unifiée et effectuer un nettoyage de build par la suite.

> [!IMPORTANT]
> Si vous avez une erreur dans le formulaire _« erreur 3 ne peut pas inclure 'monotouch.dll' et 'Xamarin.iOS.dll' dans le même projet Xamarin.iOS : 'Xamarin.iOS.dll' est référencé explicitement, alors que « monotouch.dll » est référencé par ' xxx, Version = 0.0.000, Culture = neutral, PublicKeyToken = null » «_ après la conversion de votre application à l’API unifiée, il est généralement dû à avoir un composant ou un NuGet Package dans le projet qui n’a pas été mis à jour vers l’API unifiée. Vous devez supprimer le composant existant/NuGet, mise à jour vers une version qui prend en charge les API unifiée, puis effectuez une génération propre.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>L’activation de 64 bits génère des applications Xamarin.iOS

Pour une application mobile Xamarin.iOS qui a été convertie vers l’API unifiée, le développeur doit toujours autorisent la création de l’application pour les ordinateurs 64 bits à partir des Options de l’application. Consultez la **l’activation de 64 bits s’appuie de Xamarin.iOS des applications** de la [considérations relatives à la plateforme 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) génère de document pour obtenir des instructions détaillées sur l’activation de 64 bits.

## <a name="finishing-up"></a>En cours d’achèvement

Si vous choisissez d’utiliser la méthode automatique ou manuelle pour convertir les API unifiée de votre application Xamarin.iOS du modèle Classic, il existe plusieurs instances qui requièrent d’autres, une intervention manuelle. Consultez notre [conseils pour la mise à jour le Code vers l’API unifiée](~/cross-platform/macios/unified/updating-tips.md) de documents pour les problèmes connus et des solutions de contournement.

## <a name="related-links"></a>Liens associés

- [Conseils pour la mise à jour du code vers l’API unifiée](~/cross-platform/macios/unified/updating-tips.md)
- [Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/native-types-cross-platform.md)
- [Différences d’API unifiée de vs classiques](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
