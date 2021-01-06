---
title: Xamarin.Forms Forum aux questions
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e520d222029e0d29a25f16aacfb6273da52cbf9e
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940211"
---
# <a name="no-locxamarinforms-frequently-asked-questions"></a>Xamarin.Forms Forum aux questions

## <a name="how-do-i-migrate-my-app-to-no-locxamarinforms-50"></a>[Comment faire migrer mon application vers Xamarin.Forms 5,0 ?](forms5-migration.md)

La migration d’une application vers Xamarin.Forms 5,0 requiert une connaissance de ses modifications avec rupture.

## <a name="can-i-update-the-no-locxamarinforms-default-template-to-a-newer-nuget-package"></a>[Puis-je mettre à jour le Xamarin.Forms modèle par défaut vers un package NuGet plus récent ?](update-forms-template.md)

Ce guide utilise le Xamarin.Forms modèle de bibliothèque .NET standard comme exemple, mais la même méthode générale fonctionne également pour le Xamarin.Forms modèle de projet partagé.

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-no-locxamarinforms-xaml-files"></a>[Pourquoi le concepteur XAML Visual Studio ne fonctionne-t-il pas pour les Xamarin.Forms fichiers XAML ?](forms-xaml-designer.md)

Xamarin.Forms ne prend actuellement pas en charge les concepteurs visuels pour les fichiers XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedly"></a>[Erreur de build Android : échec inattendu de la tâche « LinkAssemblies »](android-linkassemblies-error.md)

Vous pouvez voir un message d’erreur `The "LinkAssemblies" task failed unexpectedly` lors de la création d’un projet Xamarin. Android qui utilise des formulaires. Cela se produit lorsque l’éditeur de liens est actif (généralement sur une version *Release* pour réduire la taille du package d’application); Cela se produit parce que les cibles Android ne sont pas mises à jour avec le Framework le plus récent.

## <a name="why-does-my-no-locxamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-error"></a>[«Pourquoi mon Xamarin.Forms . Les mappages de projet Android échouent avec COMPILETODALVIK : erreur de niveau supérieur inattendue ?»](maps-compiletodalvik-error.md)

Cette erreur peut se produire dans le bloc d’erreurs de Visual Studio pour Mac ou dans la fenêtre sortie de la génération de Visual Studio. dans les projets Android utilisant Xamarin.Forms . Mount. Il est généralement résolu en renforçant la taille du tas Java pour votre projet Xamarin. Android.
