---
title: Attributs de vérification de la netteté objective
description: Ce document décrit l’attribut [VERIFY] généré par la netteté objective. L’attribut [VERIFY] met en évidence les développeurs où ils doivent vérifier manuellement la sortie de la finesse d’objectif.
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: conceptdev
ms.author: crdun
ms.date: 01/15/2016
ms.openlocfilehash: eb4a992255fa26be1e83f27f93755e0fd8bda0a4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765689"
---
# <a name="objective-sharpie-verify-attributes"></a>Attributs de vérification de la netteté objective

Vous constaterez souvent que les liaisons produites par objective Sharp sont annotées avec l' `[Verify]` attribut. Ces attributs indiquent que vous devez _vérifier_ que la netteté objective a effectué la bonne chose en comparant la liaison avec la déclaration d’origine c/objective-c (qui sera fournie dans un commentaire au-dessus de la déclaration liée).

La vérification est recommandée pour _toutes les_ déclarations liées, mais elle est très probablement _requise_ pour les déclarations `[Verify]` annotées avec l’attribut. En effet, dans de nombreux cas, il n’y a pas assez de métadonnées dans le code source natif d’origine pour déduire comment créer au mieux une liaison. Vous devrez peut-être référencer la documentation ou des commentaires de code dans les fichiers d’en-tête pour prendre la meilleure décision de liaison.

Une fois que vous avez vérifié que la liaison est correcte ou qu’elle a été corrigée comme `[Verify]` correcte, _supprimez_ l’attribut de la liaison.

> [!IMPORTANT]
> `[Verify]`les attributs provoquent C# intentionnellement des erreurs de compilation pour vous obliger à vérifier la liaison. Vous devez supprimer l' `[Verify]` attribut lorsque vous avez consulté le code (et éventuellement corrigé).

## <a name="verify-hints-reference"></a>Vérifier la référence des indicateurs

L’argument Hint fourni à l’attribut peut être référencé en croix avec la documentation ci-dessous. La documentation pour tous `[Verify]` les attributs produits sera également fournie sur la console une fois la liaison terminée.

|`[Verify]`Évit|Description|
|---|---|
|InferredFromPreceedingTypedef|Le nom de cette déclaration a été déduit par la Convention commune qui précède immédiatement `typedef` le code source natif d’origine. Vérifiez que le nom inféré est correct, car cette Convention est ambiguë.|
|ConstantsInterfaceAssociation|Il n’existe aucun moyen de déterminer avec quelle interface Objective-C une déclaration de variable externe peut être associée. Les instances de celles-ci `[Field]` sont liées en tant que propriétés dans une interface partielle dans une interface concrète, afin de produire une API plus intuitive, en éliminant éventuellement l’interface « constantes ».|
|MethodToProperty|Une méthode objective-C a été liée C# en tant que propriété en raison d’une Convention telle que l’absence de paramètres et le retour d’une valeur (retour non void). Les méthodes telles que celles-ci doivent souvent être liées en tant que propriétés pour mettre en surface une API plus attrayante, mais parfois des faux positifs peuvent se produire et la liaison doit en fait être une méthode.|
|StronglyTypedNSArray|Un natif `NSArray*` a été lié `NSObject[]`en tant que. Il peut être possible de taper plus fortement le tableau dans la liaison en fonction des attentes définies via la documentation de l’API (par exemple, les commentaires dans le fichier d’en-tête) ou en examinant le contenu du tableau par le biais du test. Par exemple, un NSArray * contenant uniquement NSNumber * instancescan est lié comme `NSNumber[]` au lieu `NSObject[]`de.|

Vous pouvez également recevoir rapidement la documentation d’un indicateur à `sharpie verify-docs` l’aide de l’outil, par exemple :

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```
