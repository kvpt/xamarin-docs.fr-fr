---
title: PhotoKit dans Xamarin.iOS
description: Ce document décrit PhotoKit, traitant ses objets de modèle, comment interroger des données de modèle et l’enregistrement des modifications apportées à la bibliothèque de photos.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 78646a0a420820218a8c61ea34ecc5db4438a91d
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832736"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit dans Xamarin.iOS

PhotoKit est une nouvelle infrastructure qui permet aux applications d’interroger la bibliothèque d’images système et créer des interfaces utilisateur personnalisées pour afficher et modifier son contenu. Il inclut un nombre de classes qui représentent l’image et les ressources vidéo, ainsi que les collections de ressources telles que des albums et des dossiers.

## <a name="model-objects"></a>Objets de modèle

PhotoKit représente ces ressources qu’il appelle les objets de modèle. Les objets de modèle qui représentent les photos et vidéos eux-mêmes sont de type `PHAsset`. Un `PHAsset` contient des métadonnées telles que le type de média de la ressource et sa date de création.
De même, le `PHAssetCollection` et `PHCollectionList` classes contiennent respectivement les métadonnées sur les collections de ressource et les listes de la collection. Collections de ressource sont des groupes de ressources, tels que toutes les photos et vidéos pour une année donnée. De même, les listes de collection sont des groupes de ressource collections, telles que photos et vidéos regroupés par année.

## <a name="querying-model-data"></a>Interrogation des données de modèle

PhotoKit facilite pour interroger des données de modèle par le biais de différentes méthodes d’extraction. Par exemple, pour récupérer toutes les images, vous appelleriez `PFAsset.Fetch`, en passant le `PHAssetMediaType.Image` le type de média.

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

Le `PHFetchResult` instance contiendrait tous les `PFAsset` instances représentant des images. Pour obtenir les images proprement dites, vous utilisez le `PHImageManager` (ou la version mise en cache, `PHCachingImageManager`) pour effectuer une demande pour l’image en appelant `RequestImageForAsset`. Par exemple, le code suivant récupère une image pour chaque élément multimédia dans un `PHFetchResult` à afficher dans une cellule d’affichage de collection :


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

Il en résulte dans une grille d’images, comme indiqué ci-dessous :

![](photokit-images/image4.png "L’application en cours d’exécution affiche une grille d’images")
 
## <a name="saving-changes-to-the-photo-library"></a>Enregistrer les modifications de la bibliothèque de photos

Voilà comment gérer l’interrogation et la lecture des données. Vous pouvez également écrire des modifications de revenir à la bibliothèque. Étant donné que plusieurs applications concernées sont en mesure d’interagir avec la bibliothèque de photos du système, vous pouvez inscrire un observateur pour être averti des modifications à l’aide d’un PhotoLibraryObserver. Ensuite, lorsque les modifications sont effectués, votre application peut mettre à jour en conséquence. Par exemple, voici une implémentation simple pour recharger la vue de collection ci-dessus :

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
    
Pour réellement écrire les modifications à partir de votre application, vous créez une demande de modification. Chacune des classes de modèle a une classe de demande de modification associée. Par exemple, pour modifier un PHAsset, vous créez un PHAssetChangeRequest. Les étapes pour effectuer des modifications qui sont mises à jour dans la bibliothèque de photos et envoyées aux observateurs comme celui illustré ci-dessus sont :

- Effectuer l’opération de modification.
- Enregistrer les données d’image filtré à une instance de PHContentEditingOutput.
- Effectuer une demande de modification à publier le formulaire de modifications la sortie d’édition.

Voici un exemple qui écrit une modification sur une image qui applique un filtre de noir Image Core :

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
    
Lorsque l’utilisateur sélectionne le bouton, le filtre est appliqué :

![](photokit-images/image5.png "Un exemple de filtre est appliqué")
 
Et grâce à la PHPhotoLibraryChangeObserver, la modification est répercutée dans la vue de collection lorsque l’utilisateur revient en arrière :

![](photokit-images/image6.png "La modification est répercutée dans la vue de collection lorsque l’utilisateur revient en arrière")
