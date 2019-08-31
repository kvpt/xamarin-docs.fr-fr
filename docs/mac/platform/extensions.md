---
title: Prise en charge de l’extension Xamarin.Mac
description: Ce document décrit la prise en charge par Xamarin. Mac des extensions Finder, Share et Today. Il examine les limitations et les problèmes connus, des liens vers une procédure pas à pas et un exemple d’application, et fournit des conseils pour l’utilisation des extensions.
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 7143050190d3f2318a19126ad9808b9cdf9c5010
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200323"
---
# <a name="xamarinmac-extension-support"></a>Prise en charge de l’extension Xamarin.Mac

Dans Xamarin. Mac 2,10, la prise en charge a été ajoutée pour plusieurs points d’extension macOS:

- Recherche
- Partager
- Aujourd'hui

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus

Voici les limitations et les problèmes qui peuvent se produire lors du développement d’extensions dans Xamarin. Mac:

- Il n’existe actuellement aucune prise en charge du débogage dans Visual Studio pour Mac. Tout le débogage doit être effectué via **NSLog** et la **console**. Pour plus d’informations, consultez la section conseils ci-dessous.
- Les extensions doivent être contenues dans une application hôte, qui, lorsqu’elles sont exécutées une fois, s’inscrivent auprès du système. Ils doivent ensuite être activés dans la section **extension** des **Préférences système**. 
- Certains incidents d’extension peuvent déstabiliser l’application hôte et provoquer un comportement étrange. En particulier, le **Finder** et la section **Today** du **Centre de notification** peuvent devenir «coincés» et cesser de répondre. Cela a également été constaté dans les projets d’extension dans Xcode et n’est pas lié à Xamarin. Mac. Ce problème peut se produire dans le journal système (via la **console**, consultez les conseils pour plus d’informations) sur l’impression des messages d’erreur répétés. Le redémarrage de macOS s’affiche pour résoudre le problème.

<a name="Tips" />

## <a name="tips"></a>Conseils

Les conseils suivants peuvent être utiles lors de l’utilisation d’extensions dans Xamarin. Mac:

- Comme Xamarin. Mac ne prend actuellement pas en charge les extensions de débogage, l’expérience de débogage dépend principalement de `printf` l’exécution et des instructions like. Toutefois, les extensions s’exécutent dans un processus `Console.WriteLine` de bac à sable (sandbox) et n’agissent donc pas comme dans d’autres applications Xamarin. Mac. L’appel [ `NSLog` direct](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554) de génère des messages de débogage dans le journal système.
- Toutes les exceptions non interceptées bloquent le processus d’extension, en fournissant uniquement une petite quantité d’informations utiles dans le **Journal système**. L’encapsulation de code `try/catch` gênant dans un bloc (exception) avant la nouvelle levée peut être utile. `NSLog`
- Le **Journal système** est accessible à partir de l’application **console** sous**utilitaires**des **applications** > :

    [![](extensions-images/extension02.png "Journal système")](extensions-images/extension02.png#lightbox)
- Comme indiqué ci-dessus, l’exécution de l’application hôte d’extension l’inscrit auprès du système. Suppression de l’ensemble d’applications avec annulation de son inscription. 
- Si les versions «isolées» des extensions d’une application sont inscrites, utilisez la commande suivante pour les localiser (afin qu’elles puissent être supprimées):`plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Procédure pas à pas et exemple d’application

Étant donné que le développeur créera et utilisera les extensions Xamarin. Mac de la même façon que les extensions Xamarin. iOS, consultez notre documentation relative [à la présentation des extensions](~/ios/platform/extensions.md) pour plus d’informations.

Vous trouverez [ici](https://docs.microsoft.com/samples/xamarin/mac-samples/extensionsamples)un exemple de projet Xamarin. Mac contenant de petits exemples fonctionnels de chaque type d’extension.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a fait un aperçu rapide de l’utilisation des extensions dans une application Xamarin. Mac version 2,10 (et ultérieure).

## <a name="related-links"></a>Liens associés

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://docs.microsoft.com/samples/xamarin/mac-samples/extensionsamples)
- [Human Interface Guidelines pour macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
