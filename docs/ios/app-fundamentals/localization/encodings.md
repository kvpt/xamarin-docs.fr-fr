---
title: Encodages d’internationalisation dans Xamarin. iOS
description: Ce document décrit les encodages d’internationalisation dans Xamarin. iOS, aborde les encodages disponibles et explique comment les ajouter à une application.
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/28/2017
ms.openlocfilehash: c8e82f9261601db48ec48092a5f3f81394a86eec
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70763411"
---
# <a name="internationalization-encodings-in-xamarinios"></a>Encodages d’internationalisation dans Xamarin. iOS

Par défaut, tous les encodages ne sont pas inclus dans la bibliothèque de classes Xamarin. iOS.

Pour réduire la taille de l’application, Xamarin. iOS n’inclut pas d’encodage spécifique, et vous devez ordonner à mTouch d’inclure les assemblys contenant la prise en charge de l’encodage dont vous avez besoin.

Pour ce faire, sélectionnez les encodages supplémentaires à partir du volet Build/avancé iOS dans Visual Studio pour Mac ou Visual Studio :

 [![](encodings-images/00.png "Sélection des encodages supplémentaires")](encodings-images/00.png#lightbox)

 [![](encodings-images/00a.png "Sélection des encodages supplémentaires")](encodings-images/00a.png#lightbox)

Vous pouvez sélectionner l’un des éléments suivants :

- CJC : pour Chineese, japonais et coréen
- mideast: Arabe, hébreu, turc et Latin5.
- autres Cyrillique, balte, vietnamien, ukrainien et thaï
- rares Encodages EBCDIC et autres pages de codes rares
- Sud Langues latines, Pâques et Europe occidentale
- all

 <a name="cjk" />

## <a name="cjk"></a>cjk

- CP51932
- CP932
- CP936
- CP949
- CP950
- CP54936

 <a name="mideast" />

## <a name="mideast"></a>mideast

- CP1254
- CP1255
- CP1256
- CP28596
- CP28598
- CP28599
- CP38598

 <a name="other" />

## <a name="other"></a>other

- CP1251
- CP1257
- CP1258
- CP20866
- CP21866
- CP28594
- CP28595
- CP57002
- CP874

 <a name="rare" />

## <a name="rare"></a>rares

- CP1026
- CP1047
- CP1140
- CP1141
- CP1142
- CP1143
- CP1144
- CP1145
- CP1146
- CP1147
- CP1148
- CP1149
- CP20273
- CP20277
- CP20278
- CP20280
- CP20284
- CP20285
- CP20290
- CP20297
- CP20420
- CP20424
- CP20871
- CP21025
- CP37
- CP500
- CP708
- CP852
- CP855
- CP857
- CP858
- CP862
- CP864
- CP866
- CP869
- CP870
- CP875

 <a name="west" />

## <a name="west"></a>Sud

- CP10000
- CP10079
- CP1250
- CP1252
- CP1253
- CP28592
- CP28593
- CP28597
- CP28605
- CP437
- CP850
- CP860
- CP861
- CP863
- CP865
