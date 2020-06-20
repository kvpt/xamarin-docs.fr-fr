---
title: Xamarin.FormsForum aux questions
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: edd6cfefe18ff3d5cc97ec58f3bce867f11df7c8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135874"
---
# <a name="xamarinforms-frequently-asked-questions"></a>Xamarin.FormsForum aux questions

## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[Puis-je mettre à jour le Xamarin.Forms modèle par défaut vers un package NuGet plus récent ?](update-forms-template.md)
Ce guide utilise le Xamarin.Forms modèle de bibliothèque .NET standard comme exemple, mais la même méthode générale fonctionne également pour le Xamarin.Forms modèle de projet partagé.

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[Pourquoi le concepteur XAML Visual Studio ne fonctionne-t-il pas pour les Xamarin.Forms fichiers XAML ?](forms-xaml-designer.md)
Xamarin.Formsne prend actuellement pas en charge les concepteurs visuels pour les fichiers XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedly"></a>[Erreur de build Android : échec inattendu de la tâche « LinkAssemblies »](android-linkassemblies-error.md)
Vous pouvez voir un message d’erreur `The "LinkAssemblies" task failed unexpectedly` lors de la création d’un projet Xamarin. Android qui utilise des formulaires. Cela se produit lorsque l’éditeur de liens est actif (généralement sur une version *Release* pour réduire la taille du package d’application); Cela se produit parce que les cibles Android ne sont pas mises à jour avec le Framework le plus récent. 

## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>[«Pourquoi mon Xamarin.Forms . Les mappages de projet Android échouent avec COMPILETODALVIK : erreur de niveau supérieur inattendue ?»](maps-compiletodalvik-error.md)
Cette erreur peut se produire dans le bloc d’erreurs de Visual Studio pour Mac ou dans la fenêtre sortie de la génération de Visual Studio. dans les projets Android utilisant Xamarin.Forms . Mount. Il est généralement résolu en renforçant la taille du tas Java pour votre projet Xamarin. Android.
