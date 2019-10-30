---
title: Utilisation de ressources Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 9c8db5ad7bcb012befb2fa8dcd1ecd13fa355a55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025428"
---
# <a name="using-android-assets"></a>Utilisation de ressources Android

Les _ressources_ permettent d’inclure des fichiers arbitraires tels que du texte, du XML, des polices, de la musique et des vidéos dans votre application. Si vous essayez d’inclure ces fichiers en tant que « ressources », Android les traite dans son système de ressources et vous ne pouvez pas récupérer les données brutes. Si vous souhaitez accéder aux données détouchées, les ressources sont un moyen d’y parvenir.

Les ressources ajoutées à votre projet s’affichent comme un système de fichiers que vous pouvez lire à partir de votre application à l’aide de [AssetManager](xref:Android.Content.Res.AssetManager).
Dans cette démonstration simple, nous allons ajouter une ressource de fichier texte à notre projet, la lire à l’aide de `AssetManager`et l’afficher dans un TextView.

## <a name="add-asset-to-project"></a>Ajouter un élément multimédia au projet

Les ressources sont placées dans le dossier `Assets` de votre projet. Ajoutez un nouveau fichier texte à ce dossier appelé `read_asset.txt`. Placez-y du texte tel que « je provienne d’un élément multimédia ! ».

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio doit avoir défini l' **action de génération** pour ce fichier sur **AndroidAsset**:

![Définition de l’action de génération sur AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Visual Studio pour Mac devez avoir défini l' **action de génération** pour ce fichier sur **AndroidAsset**:

[![de la définition de l’action de génération sur AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

La sélection de l’option **BuildAction** correcte permet de s’assurer que le fichier sera empaqueté dans le apk au moment de la compilation.

## <a name="reading-assets"></a>Lecture des ressources

Les ressources sont lues à l’aide d’un [AssetManager](xref:Android.Content.Res.AssetManager). Une instance du `AssetManager` est disponible en accédant à la propriété des [ressources](xref:Android.Content.Context.Assets) sur un `Android.Content.Context`, tel qu’une activité.
Dans le code suivant, nous allons ouvrir notre ressource **read_asset. txt** , lire son contenu et l’afficher à l’aide d’un TextView.

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Create a new TextView and set it as our view
    TextView tv = new TextView (this);
    
    // Read the contents of our asset
    string content;
    AssetManager assets = this.Assets;
    using (StreamReader sr = new StreamReader (assets.Open ("read_asset.txt")))
    {
        content = sr.ReadToEnd ();
    }

    // Set TextView.Text to our asset content
    tv.Text = content;
    SetContentView (tv);
}
```

## <a name="running-the-application"></a>Exécution de l'application

Exécutez l’application et les éléments suivants doivent s’afficher :

![Exemple de capture d’écran](android-assets-images/screenshot.png)

## <a name="related-links"></a>Liens associés

- [AssetManager](xref:Android.Content.Res.AssetManager)
- [Contexte](xref:Android.Content.Context)
