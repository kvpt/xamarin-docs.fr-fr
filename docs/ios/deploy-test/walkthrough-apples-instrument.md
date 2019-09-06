---
title: 'Procédure pas à pas : Utilisation de l’outil Instruments d’Apple'
description: Cet article explique comment utiliser l’outil Instruments d’Apple pour diagnostiquer les problèmes de mémoire dans une application iOS conçue avec Xamarin. Il montre comment lancer Instruments, prendre des instantanés du tas, analyser la croissance de la mémoire et bien plus encore.
ms.prod: xamarin
ms.assetid: 8f21db1d-7107-4158-8058-d47e417689a0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 2bdb95c73ed692b3ba7f0c3ff15cd7754a7e7b66
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278875"
---
# <a name="walkthrough---using-apples-instruments-tool"></a>Procédure pas à pas : Utilisation de l’outil Instruments d’Apple

_Cet article explique pas à pas comment utiliser l’outil Instruments d’Apple pour diagnostiquer les problèmes de mémoire dans une application iOS conçue avec Xamarin. Il montre comment lancer Instruments, prendre des instantanés du tas et analyser la croissance de la mémoire. Il explique également comment utiliser Instruments pour afficher et identifier les lignes de code exactes qui sont à l’origine du problème de mémoire._

Cette page montre comment utiliser l’**outil Instruments dans Xcode** pour diagnostiquer un problème de mémoire dans une application iOS.
Commencez par télécharger l’[exemple MemoryDemo](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo) et ouvrez la solution **before** dans Visual Studio pour Mac.

## <a name="diagnosing-the-memory-issues"></a>Diagnostiquer les problèmes de mémoire

1. Dans Visual Studio pour Mac, lancez **Instruments** à partir de l’élément de menu **Outils > Lancer Instruments**.
2. Chargez l’application sur l’appareil en choisissant l’élément de menu **Exécuter > Charger sur l’appareil**.
3. Choisissez le modèle **Allocations** (icône orange avec une boîte blanche)

    ![](walkthrough-apples-instrument-images/00-allocations-tempate.png "Choisir le modèle Allocations")

4. Sélectionnez l’application **MemoryDemo** dans la liste **Choose a profiling template for:** en haut de la fenêtre. Cliquez sur l’appareil iOS pour développer le menu qui affiche les applications installées.

    ![](walkthrough-apples-instrument-images/01-mem-demo.png "Sélectionner l’application MemoryDemo")

5. Appuyez sur le bouton **Choose** (en bas à droite de la fenêtre) pour démarrer **Instruments**. Ce modèle affiche deux éléments dans le volet supérieur : Allocations et VM Tracker.

6. Appuyez sur le bouton d’**enregistrement** (bouton rouge en haut à gauche) dans Instruments pour lancer l’application.

7. Sélectionnez la ligne **VM Tracker** dans le volet supérieur (comme l’application est maintenant démarrée, le volet contient deux sections : Dirty et Resident Size). Dans le volet **Inspector**, choisissez l’option **Show Display Settings** (l’icône d’engrenage), puis cochez la case **Automatic Snapshotting** figurant en bas à droite dans cette capture d’écran :

    ![](walkthrough-apples-instrument-images/02-auto-snapshot.png "Choisir l’option Show Display Settings (icône d’engrenage) et cocher la case Automatic Snapshotting")

8. Sélectionnez la ligne **Allocations** dans le volet supérieur (comme l’application est maintenant démarrée, le volet affiche *All Heap and Anonymous VM*)
9. Dans le volet **Inspector**, choisissez l’option **Show Display Settings** (l’icône d’engrenage), puis appuyez sur le bouton **Mark Generation** pour établir une base de référence. Un petit drapeau rouge apparaît dans la chronologie en haut de la fenêtre
10. Faites défiler l’application, puis resélectionnez **Mark Generation** (répétez l’opération plusieurs fois)
11. Cliquez sur le bouton d’**arrêt**.
12. Développez le nœud **Generation** avec la plus grande valeur **Growth** et triez les valeurs **Growth** par ordre décroissant.
13. Dans le volet **Inspector**, passez à la vue **Extended Detail** (la « E ») pour afficher la trace de pile sous **Stack Trace**.

14. Notez que le nœud **&lt;non-object>** affiche une croissance excessive de la mémoire. Cliquez sur la flèche à côté de ce nœud pour afficher plus de détails. Cliquez avec le bouton droit dans la trace de pile pour ajouter l’**emplacement source** dans le volet :

    ![](walkthrough-apples-instrument-images/03-mem-growth.png "Ajouter l’emplacement source dans le volet")

15. Triez par **taille** et affichez la vue détaillée **Extended Detail** :

    ![](walkthrough-apples-instrument-images/04-extended-detail.png "Trier par taille et afficher la vue Extended Detail")

16. Cliquez sur l’entrée souhaitée dans la pile des appels pour afficher le code connexe :

    ![](walkthrough-apples-instrument-images/05-related-code.png "Afficher le code connexe")

Dans ce cas, une nouvelle image est créée et stockée dans une collection pour chaque cellule, et les cellules d’affichage de collection existantes ne sont pas réutilisées.

## <a name="resolving-the-memory-issues"></a>Résoudre les problèmes de mémoire

Il est possible de résoudre ces problèmes et de réexécuter l’application à l’aide de l’outil Instruments.

Déclarez une instance unique au niveau de la classe. De cette façon, l’image peut être réutilisée, et l’objet cellule peut être réutilisé à partir d’un pool existant au lieu d’être systématiquement créé, comme illustré ci-après :

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Dequeue a cell from the reuse pool
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);

    // Reuse the image declared at the class level
    imageCell.ImageView.Image = image;

    return imageCell;
}
```

Maintenant, la quantité de mémoire utilisée pendant l’exécution de l’application est considérablement réduite. La **croissance** entre les générations se mesure désormais en Kib (kilo-octets), et non plus en MiB (mégaoctets) comme c’était le cas avant de corriger le code :

![](walkthrough-apples-instrument-images/06-reduced-memory.png "Affichage de l’utilisation de la mémoire par l’application")

Le code amélioré est disponible dans l’[exemple MemoryDemo](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo), dans la solution **after** dans Visual Studio pour Mac.

Ce blog de communauté sur le [garbage collection Xamarin.iOS](http://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/) est une source de référence utile pour gérer les problèmes de mémoire avec Xamarin.iOS.

## <a name="summary"></a>Récapitulatif

Cet article vous a montré comment utiliser Instruments pour diagnostiquer les problèmes de mémoire.
Il vous a expliqué comment lancer Instruments à partir de Visual Studio pour Mac, charger le modèle d’allocation de mémoire et identifier les problèmes de mémoire.
Enfin, l’application a été réexaminée pour vérifier que le problème a bien été corrigé.

## <a name="related-links"></a>Liens associés

- [Échantillon MemoryDemo](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo)
- [Garbage collection Xamarin.iOS (billet de blog)](http://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/)
