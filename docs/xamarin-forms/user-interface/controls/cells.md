---
title: :::no-loc(Xamarin.Forms):::Piles
description: ':::no-loc(Xamarin.Forms):::des cellules peuvent être ajoutées à ListViews et TableViews. Cet article répertorie les cellules incluses dans :::no-loc(Xamarin.Forms)::: .'
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: 21fca31ca9e1cf39843e04c3c381bb41ef77335f
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997278"
---
# <a name="no-locxamarinforms-cells"></a>:::no-loc(Xamarin.Forms):::Piles

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_:::no-loc(Xamarin.Forms):::des cellules peuvent être ajoutées à ListViews et TableViews._

Une *cellule* est un élément spécialisé utilisé pour les éléments d’une table et décrit comment chaque élément d’une liste doit être affiché. La [`Cell`](xref::::no-loc(Xamarin.Forms):::.Cell) classe dérive de [`Element`](xref::::no-loc(Xamarin.Forms):::.Element) , qui [`VisualElement`](xref::::no-loc(Xamarin.Forms):::.Element) dérive également de. Une cellule n’est pas elle-même un élément visuel ; à la place, il s’agit d’un modèle pour créer un élément visuel.

`Cell`est utilisé exclusivement avec [`ListView`](xref::::no-loc(Xamarin.Forms):::.ListView) les [`TableView`](xref::::no-loc(Xamarin.Forms):::.TableView) contrôles et. Pour en savoir plus sur l’utilisation et la personnalisation des cellules, reportez-vous à la [`ListView`](~/xamarin-forms/user-interface/listview/index.md) [`TableView`](~/xamarin-forms/user-interface/tableview.md) documentation et.

## <a name="cells"></a>Cellules

:::no-loc(Xamarin.Forms):::prend en charge les types de cellules suivants :

| Type | Description | Apparence |
| --- | --- | --- |
| `TextCell` | Un [`TextCell`](xref::::no-loc(Xamarin.Forms):::.TextCell) affiche une ou deux chaînes de texte. Définissez la [`Text`](xref::::no-loc(Xamarin.Forms):::.TextCell.Text) propriété et, éventuellement, la [`Detail`](xref::::no-loc(Xamarin.Forms):::.TextCell.Detail) propriété avec ces chaînes de texte.<br /><br />Documentation sur les [API](xref::::no-loc(Xamarin.Forms):::.TextCell)  /  [Guide](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![Exemple TextCell](cells-images/TextCell.png "Exemple TextCell")](cells-images/TextCell-Large.png#lightbox "Exemple TextCell")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
| `ImageCell` | Le [`ImageCell`](xref::::no-loc(Xamarin.Forms):::.ImageCell) affiche les mêmes informations que, [`TextCell`](xref::::no-loc(Xamarin.Forms):::.TextCell) mais comprend une bitmap que vous définissez avec la [`Source`](xref::::no-loc(Xamarin.Forms):::.Image.Source) propriété.<br /><br />Documentation sur les [API](xref::::no-loc(Xamarin.Forms):::.ImageCell)  /  [Guide](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![Exemple ImageCell](cells-images/ImageCell.png "Exemple ImageCell")](cells-images/ImageCell-Large.png#lightbox "Exemple ImageCell")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
| `SwitchCell` | Le [`SwitchCell`](xref::::no-loc(Xamarin.Forms):::.SwitchCell) contient le jeu de texte avec la [`Text`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.Text) propriété et un commutateur activé/désactivé initialement défini avec la [`On`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.On) propriété booléenne. Gérez l' [`OnChanged`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.OnChanged) événement à notifier lorsque la `On` propriété change.<br /><br />Documentation sur les [API](xref::::no-loc(Xamarin.Forms):::.SwitchCell)  /  [Guide](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Exemple SwitchCell](cells-images/SwitchCell.png "Exemple SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "Exemple SwitchCell")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
| `EntryCell` | [`EntryCell`](xref::::no-loc(Xamarin.Forms):::.EntryCell)Définit une [`Label`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Label) propriété qui identifie la cellule et une seule ligne de texte modifiable dans la [`Text`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Text) propriété. Gérez l' [`Completed`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Completed) événement à notifier lorsque l’utilisateur a terminé l’entrée de texte.<br /><br />Documentation sur les [API](xref::::no-loc(Xamarin.Forms):::.EntryCell)  /  [Guide](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Exemple EntryCell](cells-images/EntryCell.png "Exemple EntryCell")](cells-images/EntryCell-Large.png#lightbox "Exemple EntryCell")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
| | | |

## <a name="related-links"></a>Liens connexes

- [:::no-loc(Xamarin.Forms):::Exemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [:::no-loc(Xamarin.Forms):::Extraits](https://docs.microsoft.com/samples/browse/?products=xamarin&term=:::no-loc(Xamarin.Forms):::)
- [:::no-loc(Xamarin.Forms):::Documentation sur les API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
