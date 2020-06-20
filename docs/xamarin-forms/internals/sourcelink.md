---
title: Lien source avecXamarin.Forms
description: Cet article explique comment utiliser le lien source pour déboguer dans Xamarin.Forms .
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetId: 1E13FCD9-5607-46E8-80E4-87A58B389BEB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 57db314538c42ef9d58691ba16ab68371ff092b7
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138299"
---
# <a name="source-link-with-xamarinforms"></a>Lien source avecXamarin.Forms

Xamarin.FormsLes packages NuGet incluent les mappages de lien source. Le lien source mappe des bibliothèques compilées, contenues dans un package NuGet, à un référentiel de code source. Visual Studio télécharge les fichiers de code source pendant le débogage et permet aux développeurs d’effectuer un pas à pas détaillé dans le code, en activant le débogage des packages sans générer à partir de la source.

Pour plus d’informations sur l’utilisation du lien source, consultez [la documentation relative au lien source](/dotnet/standard/library-guidance/sourcelink).

::: zone pivot="windows"

> [!WARNING]
> Visual Studio 2019 prend en charge le lien source pour le **débogueur .net** , mais ne prend pas actuellement en charge le lien source pour le **débogueur mono**. Par conséquent, vous pouvez utiliser le lien source pour déboguer les applications UWP, mais pas l’application Android ou iOS. Lors du débogage d’applications UWP, vous devez vous assurer que les fichiers PDB des bibliothèques que vous souhaitez déboguer sont copiés dans le dossier **AppX** du répertoire **bin** où votre application est compilée.

## <a name="enable-source-link"></a>Activer le lien source

L’utilisation du lien source nécessite l’activation du débogage pour le code externe ; sinon, le débogueur effectue un pas à pas détaillé dans les appels au code qui n’est pas contenu dans la solution actuelle. Dans Visual Studio 2019, vous pouvez trouver ce qui se trouve dans le menu **options** de la section **débogage** :

[![Activer le lien source dans Visual Studio 2019](sourcelink-images/sourcelink-enable-pc-cropped.png)](sourcelink-images/sourcelink-enable-pc.png#lightbox)

Assurez-vous que l’option **activer uniquement mon code** est désactivée et que **activer la prise en charge du lien source** est activé.

::: zone-end
::: zone pivot="macos"

## <a name="enable-source-link"></a>Activer le lien source

L’utilisation du lien source nécessite l’activation du débogage pour le code externe ; sinon, le débogueur effectue un pas à pas détaillé dans les appels au code qui n’est pas contenu dans la solution actuelle. Cette option se trouve dans la fenêtre **Préférences** de la section **débogueur** :

[![Activer le lien source dans Visual Studio pour Mac](sourcelink-images/sourcelink-enable-mac-cropped.png)](sourcelink-images/sourcelink-enable-mac.png#lightbox)

Vérifiez que **pas à pas détaillé dans le code externe** est activé.

::: zone-end

## <a name="debug-xamarinforms-using-source-link"></a>Déboguer Xamarin.Forms à l’aide du lien source

Si le débogage de packages externes est activé, Visual Studio utilise les mappages de lien source contenus dans le package NuGet pour télécharger et effectuer un pas à pas détaillé dans le code source externe. Vous pouvez tester cela en définissant un point d’arrêt sur un appel à une méthode fournie par Xamarin.Forms :

[![Point d’arrêt défini sur la Xamarin.Forms méthode](sourcelink-images/breakpoint-cropped.png)](sourcelink-images/external-code-available.png#lightbox)

Selon les paramètres que vous avez spécifiés dans les options du **débogueur** , Visual Studio vous avertit qu’il télécharge les fichiers sources :

[![Avertissement sur le code externe Visual Studio](sourcelink-images/external-code-cropped.png)](sourcelink-images/external-code-available.png#lightbox)

Une fois que vous permettez à Visual Studio de télécharger les fichiers, le débogueur effectue un pas à pas détaillé dans le code externe.

::: zone pivot="windows"

## <a name="source-link-caching"></a>Mise en cache du lien source

Le lien source utilise la mise en cache pour les performances. Le répertoire de mise en cache pour le lien source est défini dans le menu **options** sous **débogage** dans la section **symboles** :

[![Mise en cache des liens source de Visual Studio](sourcelink-images/sourcelink-caching-pc-cropped.png)](sourcelink-images/sourcelink-caching-pc.png#lightbox)

Ce menu vous permet de spécifier le répertoire de mise en cache pour tous les symboles de débogage, ainsi que d’effacer le cache si vous rencontrez des problèmes avec les symboles mis en cache.

::: zone-end
::: zone pivot="macos"

## <a name="source-link-caching"></a>Mise en cache du lien source

Le lien source utilise la mise en cache pour les performances. Le répertoire de mise en cache pour le lien source sur MacOS est `/Users/<username>/Library/Caches/VisualStudio/8.0/Symbols` . Ce dossier contient les sous-dossiers qui stockent le référentiel utilisé pour télécharger les fichiers sources. Si le référentiel de stockage d’un package NuGet a changé, vous devrez peut-être supprimer manuellement ces dossiers pour actualiser le cache.

::: zone-end

## <a name="related-links"></a>Liens connexes

- [Documentation sur le lien source](/dotnet/standard/library-guidance/sourcelink)
- [Lien source sur GitHub](https://github.com/dotnet/sourcelink)
