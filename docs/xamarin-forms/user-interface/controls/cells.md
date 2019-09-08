---
title: Cellules Xamarin.Forms
description: Cellules Xamarin.Forms peuvent être ajoutés à ListViews et TableViews. Cet article répertorie les cellules incluses dans Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 769ff7efedca453734b6db2dd2dcb2ad503ba272
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759798"
---
# <a name="xamarinforms-cells"></a>Cellules Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Cellules Xamarin.Forms peuvent être ajoutés à ListViews et TableViews._

Un *cellule* est un élément spécialisé utilisé pour les éléments dans une table et décrit comment chaque élément dans une liste doit être restitué. Le [ `Cell` ](xref:Xamarin.Forms.Cell) dérive de la classe [ `Element` ](xref:Xamarin.Forms.Element), à partir de laquelle [ `VisualElement` ](xref:Xamarin.Forms.Element) dérive également. Une cellule n’est pas lui-même un élément visuel ; Il s’agit à la place un modèle pour la création d’un élément visuel.

`Cell` est utilisé exclusivement avec [ `ListView` ](views.md#listView) et [ `TableView` ](views.md#tableView) contrôles. Pour savoir comment utiliser et personnaliser des cellules, reportez-vous à la [ `ListView` ](~/xamarin-forms/user-interface/listview/index.md) et [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) documentation.

## <a name="cells"></a>Cellules

Xamarin.Forms prend en charge les types suivants de la cellule :

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| Un [ `TextCell` ](xref:Xamarin.Forms.TextCell) affiche une ou deux chaînes de texte. Définir le [ `Text` ](xref:Xamarin.Forms.TextCell.Text) propriété et, éventuellement, le [ `Detail` ](xref:Xamarin.Forms.TextCell.Detail) propriété ces chaînes de texte.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.TextCell) / [Guide](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![Exemple de TextCell](cells-images/TextCell.png "TextCell exemple")](cells-images/TextCell-Large.png#lightbox "TextCell exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| Le [ `ImageCell` ](xref:Xamarin.Forms.ImageCell) affiche les mêmes informations que [ `TextCell` ](#textCell) mais inclut une image bitmap que vous définissez avec le [ `Source` ](xref:Xamarin.Forms.Image.Source) propriété.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.ImageCell) / [Guide](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![Exemple de ImageCell](cells-images/ImageCell.png "ImageCell exemple")](cells-images/ImageCell-Large.png#lightbox "ImageCell exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| Le [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) contient le jeu de texte [`Text`](xref:Xamarin.Forms.SwitchCell.Text) avec la propriété et un commutateur activé/désactivé initialement défini avec [`On`](xref:Xamarin.Forms.SwitchCell.On) la propriété booléenne. Gérer le [ `OnChanged` ](xref:Xamarin.Forms.SwitchCell.OnChanged) événement à être notifié lorsque le `On` les modifications de propriété.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.SwitchCell) / [Guide](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Exemple de SwitchCell](cells-images/SwitchCell.png "SwitchCell exemple")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| Le [ `EntryCell` ](xref:Xamarin.Forms.EntryCell) définit un [ `Label` ](xref:Xamarin.Forms.EntryCell.Label) propriété qui identifie la cellule et une seule ligne de texte modifiable dans le [ `Text` ](xref:Xamarin.Forms.EntryCell.Text) propriété. Gérer le [ `Completed` ](xref:Xamarin.Forms.EntryCell.Completed) événement pour être averti lorsque l’utilisateur a terminé la saisie de texte.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.EntryCell) / [Guide](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Exemple de EntryCell](cells-images/EntryCell.png "EntryCell exemple")](cells-images/EntryCell-Large.png#lightbox "EntryCell exemple")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Liens associés

- [Exemple de Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentation sur les API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
