---
title: PhotoKit dans Xamarin. iOS
description: Ce document décrit PhotoKit, discute de ses objets Model, comment interroger les données du modèle et enregistre les modifications apportées à la bibliothèque de photos.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 5e5cc20e9fbeaf2b00e022ccdbf67286aed6d5ef
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528808"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit dans Xamarin. iOS

PhotoKit est une nouvelle infrastructure qui permet aux applications d’interroger la bibliothèque d’images système et de créer des interfaces utilisateur personnalisées pour afficher et modifier son contenu. Il comprend un certain nombre de classes qui représentent les ressources d’images et de vidéos, ainsi que des collections d’éléments multimédias tels que des albums et des dossiers.

## <a name="model-objects"></a>Objets de modèle

PhotoKit représente ces ressources dans ce qu’elle appelle des objets de modèle. Les objets de modèle qui représentent les photos et les vidéos elles- `PHAsset`mêmes sont de type. Un `PHAsset` contient des métadonnées telles que le type de média de la ressource et sa date de création.
De même, les `PHAssetCollection` classes `PHCollectionList` et contiennent respectivement des métadonnées sur les collections de ressources et les listes de collections. Les regroupements de ressources sont des groupes de ressources, tels que toutes les photos et vidéos pour une année donnée. De même, les listes de collections sont des groupes de regroupements de ressources, tels que des photos et des vidéos regroupées par année.

## <a name="querying-model-data"></a>Interrogation des données de modèle

PhotoKit facilite l’interrogation des données de modèle par le biais de diverses méthodes d’extraction. Par exemple, pour récupérer toutes les images, vous devez `PFAsset.Fetch`appeler, en `PHAssetMediaType.Image` passant le type de média.

```csharp
PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);
```

L' `PHFetchResult` instance contient alors toutes les `PFAsset` instances représentant les images. Pour obtenir les images elles-mêmes, vous `PHImageManager` utilisez (ou la `PHCachingImageManager`version de mise en cache) pour effectuer une demande pour l' `RequestImageForAsset`image en appelant. Par exemple, le code suivant récupère une image pour chaque élément multimédia d’un `PHFetchResult` à afficher dans une cellule d’affichage de collection:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
    imageMgr.RequestImageForAsset (
        (PHAsset)fetchResults [(uint)indexPath.Item],
        thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (),
        (img, info) => {
            imageCell.ImageView.Image = img;
        }
    );
    return imageCell;
}
```

Cela génère une grille d’images comme indiqué ci-dessous:

![](photokit-images/image4.png "L’application en cours d’exécution affichant une grille d’images")

## <a name="saving-changes-to-the-photo-library"></a>Enregistrement des modifications apportées à la bibliothèque de photos

Voilà comment gérer l’interrogation et la lecture de données. Vous pouvez également réécrire les modifications dans la bibliothèque. Étant donné que plusieurs applications intéressées sont en mesure d’interagir avec la bibliothèque de photos système, vous pouvez inscrire un observateur pour être averti des modifications à l’aide d’un PhotoLibraryObserver. Ensuite, lorsque des modifications sont apportées, votre application peut être mise à jour en conséquence. Par exemple, voici une implémentation simple pour recharger la vue de collection ci-dessus:

```csharp
class PhotoLibraryObserver : PHPhotoLibraryChangeObserver
{
    readonly PhotosViewController controller;
    public PhotoLibraryObserver (PhotosViewController controller)

    {
        this.controller = controller;
    }

    public override void PhotoLibraryDidChange (PHChange changeInstance)
    {
        DispatchQueue.MainQueue.DispatchAsync (() => {
        var changes = changeInstance.GetFetchResultChangeDetails (controller.fetchResults);
        controller.fetchResults = changes.FetchResultAfterChanges;
        controller.CollectionView.ReloadData ();
        });
    }
}
```

Pour réécrire des modifications à partir de votre application, vous créez une demande de modification. Chacune des classes de modèle est associée à une classe de demande de modification. Par exemple, pour modifier un PHAsset, vous créez un PHAssetChangeRequest. Les étapes permettant d’effectuer des modifications qui sont réécrites dans la bibliothèque de photos et envoyées à des observateurs comme celui ci-dessus sont les suivantes:

- Effectuez l’opération de modification.
- Enregistrez les données d’image filtrées dans une instance PHContentEditingOutput.
- Effectuez une demande de modification pour publier les modifications dans le résultat de la modification.

Voici un exemple qui réécrit une modification apportée à une image qui applique un filtre noir d’image principale:

```csharp
void ApplyNoirFilter (object sender, EventArgs e)
{

    Asset.RequestContentEditingInput (new PHContentEditingInputRequestOptions (), (input, options) => {

        // perform the editing operation, which applies a noir filter in this case
        var image = CIImage.FromUrl (input.FullSizeImageUrl);
        image = image.CreateWithOrientation((CIImageOrientation)input.FullSizeImageOrientation);
        var noir = new CIPhotoEffectNoir {
            Image = image
        };
        var ciContext = CIContext.FromOptions (null);
        var output = noir.OutputImage;
        var uiImage = UIImage.FromImage (ciContext.CreateCGImage (output, output.Extent));
        imageView.Image = uiImage;
        //
        // save the filtered image data to a PHContentEditingOutput instance
        var editingOutput = new PHContentEditingOutput(input);
        var adjustmentData = new PHAdjustmentData();
        var data = uiImage.AsJPEG();
        NSError error;
        data.Save(editingOutput.RenderedContentUrl, false, out error);
        editingOutput.AdjustmentData = adjustmentData;
        //
        // make a change request to publish the changes form the editing output
        PHPhotoLibrary.GetSharedPhotoLibrary.PerformChanges (() => {
            PHAssetChangeRequest request = PHAssetChangeRequest.ChangeRequest(Asset);
            request.ContentEditingOutput = editingOutput;
        },
        (ok, err) => Console.WriteLine ("photo updated successfully: {0}", ok));
    });
}
```

Lorsque l’utilisateur sélectionne le bouton, le filtre est appliqué:

![](photokit-images/image5.png "Exemple de filtre appliqué")

Et grâce à PHPhotoLibraryChangeObserver, la modification est reflétée dans la vue de collection lorsque l’utilisateur navigue vers l’arrière:

![](photokit-images/image6.png "La modification est reflétée dans la vue de collection lorsque l’utilisateur navigue vers l’arrière")
