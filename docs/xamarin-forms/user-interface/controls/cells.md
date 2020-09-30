---
title: Xamarin.Forms Piles
description: Xamarin.Forms des cellules peuvent être ajoutées à ListViews et TableViews. Cet article répertorie les cellules incluses dans Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be5b2854c2f546e2194bd4dc95db77c9dcaf0760
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562806"
---
# <a name="no-locxamarinforms-cells"></a>Xamarin.Forms Piles

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Xamarin.Forms des cellules peuvent être ajoutées à ListViews et TableViews._

Une *cellule* est un élément spécialisé utilisé pour les éléments d’une table et décrit comment chaque élément d’une liste doit être affiché. La [`Cell`](xref:Xamarin.Forms.Cell) classe dérive de [`Element`](xref:Xamarin.Forms.Element) , qui [`VisualElement`](xref:Xamarin.Forms.Element) dérive également de. Une cellule n’est pas elle-même un élément visuel ; à la place, il s’agit d’un modèle pour créer un élément visuel.

`Cell` est utilisé exclusivement avec [`ListView`](xref:Xamarin.Forms.ListView) les [`TableView`](xref:Xamarin.Forms.TableView) contrôles et. Pour en savoir plus sur l’utilisation et la personnalisation des cellules, reportez-vous à la [`ListView`](~/xamarin-forms/user-interface/listview/index.md) [`TableView`](~/xamarin-forms/user-interface/tableview.md) documentation et.

## <a name="cells"></a>Cellules

Xamarin.Forms prend en charge les types de cellules suivants :

| Type | Description | Apparence |
| --- | --- | --- |
| `TextCell` | Un [`TextCell`](xref:Xamarin.Forms.TextCell) affiche une ou deux chaînes de texte. Définissez la [`Text`](xref:Xamarin.Forms.TextCell.Text) propriété et, éventuellement, la [`Detail`](xref:Xamarin.Forms.TextCell.Detail) propriété avec ces chaînes de texte.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.TextCell)  /  [Guide](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![Exemple TextCell](cells-images/TextCell.png "Exemple TextCell")](cells-images/TextCell-Large.png#lightbox "Exemple TextCell")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
| `ImageCell` | Le [`ImageCell`](xref:Xamarin.Forms.ImageCell) affiche les mêmes informations que, [`TextCell`](xref:Xamarin.Forms.TextCell) mais comprend une bitmap que vous définissez avec la [`Source`](xref:Xamarin.Forms.Image.Source) propriété.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.ImageCell)  /  [Guide](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![Exemple ImageCell](cells-images/ImageCell.png "Exemple ImageCell")](cells-images/ImageCell-Large.png#lightbox "Exemple ImageCell")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
| `SwitchCell` | Le [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) contient le jeu de texte avec la [`Text`](xref:Xamarin.Forms.SwitchCell.Text) propriété et un commutateur activé/désactivé initialement défini avec la [`On`](xref:Xamarin.Forms.SwitchCell.On) propriété booléenne. Gérez l' [`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged) événement à notifier lorsque la `On` propriété change.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.SwitchCell)  /  [Guide](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Exemple SwitchCell](cells-images/SwitchCell.png "Exemple SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "Exemple SwitchCell")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
| `EntryCell` | [`EntryCell`](xref:Xamarin.Forms.EntryCell)Définit une [`Label`](xref:Xamarin.Forms.EntryCell.Label) propriété qui identifie la cellule et une seule ligne de texte modifiable dans la [`Text`](xref:Xamarin.Forms.EntryCell.Text) propriété. Gérez l' [`Completed`](xref:Xamarin.Forms.EntryCell.Completed) événement à notifier lorsque l’utilisateur a terminé l’entrée de texte.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.EntryCell)  /  [Guide](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Exemple EntryCell](cells-images/EntryCell.png "Exemple EntryCell")](cells-images/EntryCell-Large.png#lightbox "Exemple EntryCell")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
| | | |

## <a name="related-links"></a>Liens connexes

- [Xamarin.Forms Exemple FormsGallery](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Extraits](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms Documentation sur les API](/dotnet/api/xamarin.forms?view=xamarin-forms)