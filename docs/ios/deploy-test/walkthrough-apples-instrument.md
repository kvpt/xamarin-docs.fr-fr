---
title: 'Procédure pas à pas : Utilisation de l’outil Instruments d’Apple'
description: Cet article explique comment utiliser l’outil Instruments d’Apple pour diagnostiquer les problèmes de mémoire dans une application iOS conçue avec Xamarin. Il montre comment lancer Instruments, prendre des instantanés du tas, analyser la croissance de la mémoire et bien plus encore.
ms.prod: xamarin
ms.assetid: 8f21db1d-7107-4158-8058-d47e417689a0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 41254fb6aac176cd796fba851478b31f774553d2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73023456"
---
# <a name="walkthrough---using-apples-instruments-tool"></a>Procédure pas à pas : Utilisation de l’outil Instruments d’Apple

_Cet article passe par la façon d’utiliser l’outil Instruments d’Apple pour diagnostiquer les problèmes de mémoire dans une application iOS construite avec Xamarin. Il montre comment lancer des instruments, prendre des instantanés de tas et analyser la croissance de la mémoire. Il montre également comment utiliser des instruments pour afficher et identifier les lignes exactes de code qui causent le problème de mémoire._

Cette page montre comment utiliser l’**outil Instruments dans Xcode** pour diagnostiquer un problème de mémoire dans une application iOS.
Commencez par télécharger l’[exemple MemoryDemo](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo) et ouvrez la solution **before** dans Visual Studio pour Mac.

## <a name="diagnosing-the-memory-issues"></a>Diagnostiquer les problèmes de mémoire

1. Dans Visual Studio pour Mac, lancez **Instruments** à partir de l’élément de menu **Outils > Lancer Instruments**.
2. Chargez l’application sur l’appareil en choisissant l’élément de menu **Exécuter > Charger sur l’appareil**.
3. Choisissez le modèle **Allocations** (icône orange avec une boîte blanche)

    ![](walkthrough-apples-instrument-images/00-allocations-tempate.png "Choose the Allocations template")

4. Sélectionnez l’application **MemoryDemo** dans la liste **Choose a profiling template for:** en haut de la fenêtre. Cliquez sur l’appareil iOS pour développer le menu qui affiche les applications installées.

    ![](walkthrough-apples-instrument-images/01-mem-demo.png "Select the Memory Demo application")

5. Appuyez sur le bouton **Choose** (en bas à droite de la fenêtre) pour démarrer **Instruments**. Ce modèle affiche deux éléments dans le volet supérieur : Allocations et VM Tracker.

6. Appuyez sur le bouton d’**enregistrement** (bouton rouge en haut à gauche) dans Instruments pour lancer l’application.

7. Sélectionnez la ligne **VM Tracker** dans le volet supérieur (comme l’application est maintenant démarrée, le volet contient deux sections : Dirty et Resident Size). Dans le volet **Inspector**, choisissez l’option **Show Display Settings** (l’icône d’engrenage), puis cochez la case **Automatic Snapshotting** figurant en bas à droite dans cette capture d’écran :

    ![](walkthrough-apples-instrument-images/02-auto-snapshot.png "Choose the Show Display Settings option the gear icon then tick the Automatic Snapshotting checkbox")

8. Sélectionnez la ligne **Allocations** dans le volet supérieur (comme l’application est maintenant démarrée, le volet affiche *All Heap and Anonymous VM*)
9. Dans le volet **Inspector**, choisissez l’option **Show Display Settings** (l’icône d’engrenage), puis appuyez sur le bouton **Mark Generation** pour établir une base de référence. Un petit drapeau rouge apparaît dans la chronologie en haut de la fenêtre
10. Faites défiler l’application, puis resélectionnez **Mark Generation** (répétez l’opération plusieurs fois)
11. Cliquez sur le bouton d’**arrêt**.
12. Développez le nœud **Generation** avec la plus grande valeur **Growth** et triez les valeurs **Growth** par ordre décroissant.
13. Dans le volet **Inspector**, passez à la vue **Extended Detail** (la « E ») pour afficher la trace de pile sous **Stack Trace**.

14. Remarquez que le ** &lt;nœud de>non-objet** montre une croissance excessive de la mémoire. Cliquez sur la flèche à côté de ce nœud pour afficher plus de détails. Cliquez avec le bouton droit dans la trace de pile pour ajouter l’**emplacement source** dans le volet :

    ![](walkthrough-apples-instrument-images/03-mem-growth.png "Add Source Location to the pane")

15. Triez par **taille** et affichez la vue détaillée **Extended Detail** :

    ![](walkthrough-apples-instrument-images/04-extended-detail.png "Sort by Size and display the  Extended Detail view")

16. Cliquez sur l’entrée souhaitée dans la pile des appels pour afficher le code connexe :

    ![](walkthrough-apples-instrument-images/05-related-code.png "Viewing the related code")

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

![](walkthrough-apples-instrument-images/06-reduced-memory.png "Showing the app memory usage")

Le code amélioré est disponible dans l’[exemple MemoryDemo](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo), dans la solution **after** dans Visual Studio pour Mac.

Ce blog de communauté sur le [garbage collection Xamarin.iOS](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/) est une source de référence utile pour gérer les problèmes de mémoire avec Xamarin.iOS.

## <a name="summary"></a>Récapitulatif

Cet article vous a montré comment utiliser Instruments pour diagnostiquer les problèmes de mémoire.
Il vous a expliqué comment lancer Instruments à partir de Visual Studio pour Mac, charger le modèle d’allocation de mémoire et identifier les problèmes de mémoire.
Enfin, l’application a été réexaminée pour vérifier que le problème a bien été corrigé.

## <a name="related-links"></a>Liens connexes

- [Exemple MemoryDemo](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo)
- [Garbage collection Xamarin.iOS (billet de blog)](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/)
