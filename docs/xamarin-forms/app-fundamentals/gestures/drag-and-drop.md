---
title: Ajouter des modules de reconnaissance de mouvement de glisser-déplacer
description: Cet article explique comment reconnaître les gestes de glisser-déplacer avec Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 4CB2F270-908A-4A89-B852-70BC04066E8C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/04/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b21c1c85a29717bd9e5b9e9f164ce8f058764865
ms.sourcegitcommit: 579ec4f2884fa391e5e214a3952cd6004c521eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919643"
---
# <a name="add-drag-and-drop-gesture-recognizers"></a>Ajouter des modules de reconnaissance de mouvement de glisser-déplacer

![API de la version préliminaire](~/media/shared/preview.png)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/WorkingWithGestures/DragAndDropGesture)

Un mouvement de glisser-déplacer permet de faire glisser des éléments et leurs packages de données associés à partir d’un emplacement à l’écran à un autre emplacement à l’aide d’un mouvement continu. La fonction glisser-déplacer peut avoir lieu dans une application unique, ou elle peut démarrer dans une application et se terminer dans une autre.

> [!IMPORTANT]
> Le module de Xamarin.Forms reconnaissance des mouvements de glisser-déplacer est actuellement expérimental et ne peut être utilisé qu’en définissant l' `DragAndDrop_Experimental` indicateur. Pour plus d’informations, consultez la page [indicateurs expérimentaux](~/xamarin-forms/internals/experimental-flags.md).
>
> La reconnaissance des mouvements de glisser-déplacer est prise en charge sur iOS, Android et le plateforme Windows universelle (UWP). Toutefois, sur iOS, une plateforme minimale d’iOS 11 est requise.

La *source de glissement*, qui est l’élément sur lequel le mouvement de glissement est initié, peut fournir des données à transférer en remplissant un objet de package de données. Lorsque la source de glissement est relâchée, le déplacement se produit. La *cible de déplacement*, qui est l’élément sous la source de glissement, traite le package de données.

Le processus d’activation de la fonction glisser-déplacer dans une application est le suivant :

1. Activez l’opération glisser sur un élément en ajoutant un `DragGestureRecognizer` objet à sa `GestureRecognizers` collection et en affectant à la propriété la valeur `DragGestureRecognizer.CanDrag` `true` . Pour plus d’informations, consultez [activer l’opération glisser](#enable-drag).
1. facultatif Générez un package de données. Xamarin.Formsremplit automatiquement le package de données pour les contrôles d’image et de texte, mais pour d’autres contenus, vous devez construire votre propre package de données. Pour plus d’informations, consultez [créer un package de données](#build-a-data-package).
1. Activez la suppression sur un élément en ajoutant un `DropGestureRecognizer` objet `GestureRecognizers` à sa collection et en affectant à la propriété la valeur `DropGestureRecognizer.AllowDrop` `true` . Pour plus d’informations, consultez [activer Drop](#enable-drop).
1. facultatif Gérez l' `DropGestureRecognizer.DragOver` événement pour indiquer le type d’opération autorisé par la cible de déplacement. Pour plus d’informations, consultez [gérer l’événement DragOver](#handle-the-dragover-event).
1. facultatif Traitez le package de données pour recevoir le contenu supprimé. Xamarin.Formsrécupère automatiquement les données d’image et de texte à partir du package de données, mais pour tout autre contenu, vous devez traiter le package de données. Pour plus d’informations, consultez [traiter le package de données](#process-the-data-package).

> [!NOTE]
> Le fait de faire glisser des éléments vers et depuis [`CollectionView`](xref:Xamarin.Forms.CollectionView) n’est pas pris en charge actuellement.

## <a name="enable-drag"></a>Activer le glisser

Dans Xamarin.Forms , la reconnaissance de mouvement de glissement est fournie par la `DragGestureRecognizer` classe. Cette classe définit les propriétés suivantes :

- `CanDrag`, de type `bool` , qui indique si l’élément auquel le module de reconnaissance de mouvement est attaché peut être une source de glissement. La valeur par défaut de cette propriété est `false`.
- `DragStartingCommand`, de type `ICommand` , qui est exécuté lorsqu’un mouvement de glissement est reconnu pour la première fois.
- `DragStartingCommandParameter`, de type `object` : paramètre passé à la commande `DragStartingCommand`.
- `DropCompletedCommmand`, de type `ICommand` , qui est exécuté lorsque la source de glissement est supprimée.
- `DropCompletedCommandParameter`, de type `object` : paramètre passé à la commande `DropCompletedCommand`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

La `DragGestureRecognizer` classe définit également `DragStarting` les `DropCompleted` événements et. Lorsqu’un `DragGestureRecognizer` objet détecte un mouvement de glissement, il exécute `DragStartingCommand` et appelle l' `DragStarting` événement. Ensuite, lorsque l' `DragGestureRecognizer` objet détecte l’achèvement d’un mouvement de déplacement, il exécute `DropCompletedCommand` et appelle l' `DropCompleted` événement.

L' `DragStartingEventArgs` objet qui accompagne l' `DragStarting` événement définit les propriétés suivantes :

- `Handled`, de type `bool` , indique si le gestionnaire d’événements a géré l’événement ou s’il Xamarin.Forms doit continuer son propre traitement.
- `Cancel`, de type `bool` , indique si l’événement doit être annulé.
- `Data`, de type `DataPackage` , indique le package de données qui accompagne la source du glissement. Il s’agit d’une propriété en lecture seule.

L' `DropCompletedEventArgs` objet qui accompagne l' `DropCompleted` événement a une propriété en lecture seule `DropResult` , de type `DataPackageOperation` . Pour plus d’informations sur l' `DataPackageOperation` énumération, consultez [gérer l’événement DragOver](#handle-the-dragover-event).

L’exemple de code XAML suivant montre un `DragGestureRecognizer` attaché à un [`Image`](xref:Xamarin.Forms.Image) :

```xaml
<Image Source="monkeyface.png">
    <Image.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True" />
    </Image.GestureRecognizers>
</Image>
```

Dans cet exemple, un mouvement de glissement peut être initié sur [`Image`](xref:Xamarin.Forms.Image) .

> [!TIP]
> Sur iOS, Android et UWP, un mouvement de glissement est initié avec une pression longue, suivie d’un glissement.

## <a name="build-a-data-package"></a>Créer un package de données

Xamarin.Formsgénère automatiquement un package de données pour vous, quand un glisser-déplacer est initié, pour les contrôles suivants :

- Contrôles de texte. Vous pouvez faire glisser des valeurs de texte à partir d’objets,,, [`CheckBox`](xref:Xamarin.Forms.CheckBox) [`DatePicker`](xref:Xamarin.Forms.DatePicker) [`Editor`](xref:Xamarin.Forms.Editor) [`Entry`](xref:Xamarin.Forms.Entry) , [`Label`](xref:Xamarin.Forms.Label) , [`RadioButton`](xref:Xamarin.Forms.RadioButton) , [`Switch`](xref:Xamarin.Forms.Switch) et [`TimePicker`](xref:Xamarin.Forms.TimePicker) .
- Contrôles image. Vous pouvez faire glisser des images à partir de [`Button`](xref:Xamarin.Forms.Button) [`Image`](xref:Xamarin.Forms.Image) contrôles, et [`ImageButton`](xref:Xamarin.Forms.ImageButton) .

Le tableau suivant répertorie les propriétés qui sont lues et toute conversion tentée quand un glisser est initié sur un contrôle de texte :

| Control | Propriété | Conversion |
| --- | --- | --- |
| `CheckBox` | `IsChecked` | `bool` converti en `string`. |
| `DatePicker` | `Date` | `DateTime` converti en `string`. |
| `Editor` | `Text` ||
| `Entry` | `Text` ||
| `Label` | `Text` ||
| `RadioButton` | `IsChecked` | `bool` converti en `string`. |
| `Switch` | `IsToggled` | `bool` converti en `string`. |
| `TimePicker` | `Time` | `TimeSpan` converti en `string`. |

Pour du contenu autre que du texte et des images, vous devez créer un package de données vous-même.

Les packages de données sont représentés par la `DataPackage` classe, qui définit les propriétés suivantes :

- `Properties`, de type `DataPackagePropertySet` , qui est une collection de propriétés qui composent les données contenues dans le `DataPackage` . Cette propriété est une propriété en lecture seule.
- `Image`, de type [`ImageSource`](xref:Xamarin.Forms.ImageSource) , qui est l’image contenue dans `DataPackage` .
- `Text`, de type `string` , qui est le texte contenu dans le `DataPackage` .
- `View`, de type `DataPackageView` , qui est une version en lecture seule du `DataPackage` .

La `DataPackagePropertySet` classe représente un conteneur de propriétés stocké en tant que `Dictionary<string,object>` . Pour plus d’informations sur la `DataPackageView` classe, consultez [traiter le package de données](#process-the-data-package).

### <a name="store-image-or-text-data"></a>Stocker des données d’image ou de texte

Les données d’image ou de texte peuvent être associées à une source de glissement en stockant les données dans la `DataPackage.Image` `DataPackage.Text` propriété ou. Cela peut être accompli dans le gestionnaire de l' `DragStarting` événement.

L’exemple de code XAML suivant montre un `DragGestureRecognizer` qui inscrit un gestionnaire pour l' `DragStarting` événement :

```xaml
<Path Stroke="Black"
      StrokeThickness="4">
    <Path.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True"
                               DragStarting="OnDragStarting" />
    </Path.GestureRecognizers>
    <Path.Data>
        <!-- PathGeometry goes here -->
    </Path.Data>
</Path>
```

Dans cet exemple, `DragGestureRecognizer` est attaché à un `Path` objet. L' `DragStarting` événement est déclenché lorsqu’un mouvement de glissement est détecté sur le `Path` , qui exécute le `OnDragStarting` Gestionnaire d’événements :

```csharp
void OnDragStarting(object sender, DragStartingEventArgs e)
{
    e.Data.Text = "My text data goes here";
}
```

L' `DragStartingEventArgs` objet qui accompagne l' `DragStarting` événement a une `Data` propriété, de type `DataPackage` . Dans cet exemple, la `Text` propriété de l' `DataPackage` objet a la valeur `string` . `DataPackage`Est ensuite accessible lors de l’insertion, pour récupérer le `string` .

### <a name="store-data-in-the-property-bag"></a>Stocker des données dans le conteneur de propriétés

Toutes les données, y compris les images et le texte, peuvent être associées à une source de glissement en stockant les données dans la `DataPackage.Properties` collection. Cela peut être accompli dans le gestionnaire de l' `DragStarting` événement.

L’exemple de code XAML suivant montre un `DragGestureRecognizer` qui inscrit un gestionnaire pour l' `DragStarting` événement :

```xaml
<Rectangle Stroke="Red"
           Fill="DarkBlue"
           StrokeThickness="4"
           HeightRequest="200"
           WidthRequest="200">
    <Rectangle.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True"
                               DragStarting="OnDragStarting" />
    </Rectangle.GestureRecognizers>
</Rectangle>
```

Dans cet exemple, `DragGestureRecognizer` est attaché à un `Rectangle` objet. L' `DragStarting` événement est déclenché lorsqu’un mouvement de glissement est détecté sur le `Rectangle` , qui exécute le `OnDragStarting` Gestionnaire d’événements :

```csharp
void OnDragStarting(object sender, DragStartingEventArgs e)
{
    Shape shape = (sender as Element).Parent as Shape;
    e.Data.Properties.Add("Square", new Square(shape.Width, shape.Height));
}
```

L' `DragStartingEventArgs` objet qui accompagne l' `DragStarting` événement a une `Data` propriété, de type `DataPackage` . La `Properties` collection de l' `DataPackage` objet, qui est une `Dictionary<string, object>` collection, peut être modifiée pour stocker toutes les données requises. Dans cet exemple, le `Properties` dictionnaire est modifié pour stocker un `Square` objet, qui représente la taille du `Rectangle` , par rapport à une touche « carrée ».

## <a name="enable-drop"></a>Activer la suppression

Dans Xamarin.Forms , la reconnaissance de mouvement de suppression est fournie par la `DropGestureRecognizer` classe. Cette classe définit les propriétés suivantes :

- `AllowDrop`, de type `bool` , qui indique si l’élément auquel le module de reconnaissance de mouvement est attaché peut être une cible de déplacement. La valeur par défaut de cette propriété est `false`.
- `DragOverCommand`, de type `ICommand` , qui est exécuté lorsque la source de glissement est glissée sur la cible de déplacement.
- `DragOverCommandParameter`, de type `object` : paramètre passé à la commande `DragOverCommand`.
- `DropCommand`, de type `ICommand` , qui est exécuté lorsque la source de glissement est déplacée sur la cible de déplacement.
- `DropCommandParameter`, de type `object` : paramètre passé à la commande `DropCommand`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

La `DropGestureRecognizer` classe définit également `DragOver` les `Drop` événements et. Lorsqu’un `DropGestureRecognizer` reconnaît une source de glissement sur la cible de déplacement, il exécute `DragOverCommand` et appelle l' `DragOver` événement. Ensuite, lorsque le `DropGestureRecognizer` reconnaît un mouvement de déplacement sur la cible de déplacement, il exécute `DropCommand` et appelle l' `Drop` événement.

La `DragEventArgs` classe qui accompagne l' `DragOver` événement définit les propriétés suivantes :

- `Data`, de type `DataPackage` , qui contient les données associées à la source de glissement. Cette propriété est en lecture seule.
- `AcceptedOperation`, de type `DataPackageOperation` , qui spécifie les opérations autorisées par la cible de déplacement.

Pour plus d’informations sur l' `DataPackageOperation` énumération, consultez [gérer l’événement DragOver](#handle-the-dragover-event).

La `DropEventArgs` classe qui accompagne l' `Drop` événement définit les propriétés suivantes :

- `Data`, de type `DataPackageView` , qui est une version en lecture seule du package de données.
- `Handled`, de type `bool` , indique si le gestionnaire d’événements a géré l’événement ou s’il Xamarin.Forms doit continuer son propre traitement.

L’exemple de code XAML suivant montre un `DropGestureRecognizer` attaché à un [`Image`](xref:Xamarin.Forms.Image) :

```xaml
<Image BackgroundColor="Silver"
       HeightRequest="300"
       WidthRequest="250">
    <Image.GestureRecognizers>
        <DropGestureRecognizer AllowDrop="True" />
    </Image.GestureRecognizers>
</Image>
```

Dans cet exemple, quand une source de glissement est déplacée sur la [`Image`](xref:Xamarin.Forms.Image) cible de déplacement, la source de glissement est copiée dans la cible de déplacement, à condition que la source de glissement soit un [`ImageSource`](xref:Xamarin.Forms.ImageSource) . Cela se produit parce que Xamarin.Forms copie automatiquement les images glissées et le texte vers les cibles de dépôt compatibles.

## <a name="handle-the-dragover-event"></a>Gérer l’événement DragOver

L' `DropGestureRecognizer.DragOver` événement peut éventuellement être géré pour indiquer le type d’opérations autorisées par la cible de déplacement. Pour ce faire, vous devez définir la `AcceptedOperation` propriété, de type `DataPackageOperation` , de l' `DragEventArgs` objet qui accompagne l' `DragOver` événement.

L’énumération `DataPackageOperation` définit les membres suivants :

- `None`, indique qu’aucune action ne sera effectuée.
- `Copy`, indique que le contenu de la source de glissement sera copié sur la cible de déplacement.

> [!IMPORTANT]
> Lorsqu’un `DragEventArgs` objet est créé, la `AcceptedOperation` propriété a comme valeur par défaut `DataPackageOperation.Copy` .

L’exemple de code XAML suivant montre un `DropGestureRecognizer` qui inscrit un gestionnaire pour l' `DragOver` événement :

```xaml
<Image BackgroundColor="Silver"
       HeightRequest="300"
       WidthRequest="250">
    <Image.GestureRecognizers>
        <DropGestureRecognizer AllowDrop="True"
                               DragOver="OnDragOver" />
    </Image.GestureRecognizers>
</Image>
```

Dans cet exemple, `DropGestureRecognizer` est attaché à un [`Image`](xref:Xamarin.Forms.Image) objet. L' `DragOver` événement est déclenché lorsqu’une source de glissement est glissée sur la cible de déplacement, mais n’a pas été supprimée, ce qui exécute le `OnDragOver` Gestionnaire d’événements :

```csharp
void OnDragOver(object sender, DragEventArgs e)
{
    e.AcceptedOperation = DataPackageOperation.None;
}
```

Dans cet exemple, la `AcceptedOperation` propriété de l' `DragEventArgs` objet a la valeur `DataPackageOperation.None` . Cela garantit qu’aucune action n’est effectuée quand une source de glissement est déplacée sur la cible de déplacement.

## <a name="process-the-data-package"></a>Traiter le package de données

L' `Drop` événement est déclenché lorsqu’une source de glissement est libérée sur une cible de déplacement. Dans ce cas, Xamarin.Forms tente automatiquement de récupérer les données du package de données, quand une source de glissement est déposée sur les contrôles suivants :

- Contrôles de texte. Les valeurs de texte peuvent être déposées sur les objets,,, [`CheckBox`](xref:Xamarin.Forms.CheckBox) [`DatePicker`](xref:Xamarin.Forms.DatePicker) [`Editor`](xref:Xamarin.Forms.Editor) [`Entry`](xref:Xamarin.Forms.Entry) , [`Label`](xref:Xamarin.Forms.Label) ,, [`RadioButton`](xref:Xamarin.Forms.RadioButton) [`Switch`](xref:Xamarin.Forms.Switch) et [`TimePicker`](xref:Xamarin.Forms.TimePicker) .
- Contrôles image. Les images peuvent être déplacées vers des [`Button`](xref:Xamarin.Forms.Button) [`Image`](xref:Xamarin.Forms.Image) contrôles, et [`ImageButton`](xref:Xamarin.Forms.ImageButton) .

Le tableau suivant présente les propriétés qui sont définies, ainsi que toute conversion tentée, lorsqu’une source de glissement basée sur du texte est supprimée sur un contrôle de texte :

| Control | Propriété | Conversion |
| --- | --- | --- |
| `CheckBox` | `IsChecked` | `string`est converti en `bool` . |
| `DatePicker` | `Date` | `string`est converti en `DateTime` . |
| `Editor` | `Text` ||
| `Entry` | `Text` ||
| `Label` | `Text` ||
| `RadioButton` | `IsChecked` | `string`est converti en `bool` . |
| `Switch` | `IsToggled` | `string`est converti en `bool` . |
| `TimePicker` | `Time` | `string`est converti en `TimeSpan` . |

Pour du contenu autre que du texte et des images, vous devez traiter le package de données vous-même.

La `DropEventArgs` classe qui accompagne l' `Drop` événement définit une `Data` propriété, de type `DataPackageView` . Cette propriété représente une version en lecture seule du package de données.

### <a name="retrieve-image-or-text-data"></a>Récupérer des données d’image ou de texte

Les données d’image ou de texte peuvent être récupérées à partir d’un package de données dans le gestionnaire de l' `Drop` événement, à l’aide des méthodes définies dans la `DataPackageView` classe.

La `DataPackageView` classe comprend `GetImageAsync` les `GetTextAsync` méthodes et. La `GetImageAsync` méthode récupère une image à partir du package de données, qui était stocké dans la `DataPackage.Image` propriété et retourne `Task<ImageSource>` . De même, la `GetTextAsync` méthode récupère du texte à partir du package de données, qui était stocké dans la `DataPackage.Text` propriété et retourne `Task<string>` .

L’exemple suivant montre un `Drop` Gestionnaire d’événements qui récupère du texte à partir du package de données pour un `Path` :

```csharp
async void OnDrop(object sender, DropEventArgs e)
{
    string text = await e.Data.GetTextAsync();

    // Perform logic to take action based on the text value.
}
```

Dans cet exemple, les données texte sont récupérées à partir du package de données à l’aide de la `GetTextAsync` méthode. Une action basée sur la valeur de texte peut alors être prise.

### <a name="retrieve-data-from-the-property-bag"></a>Récupérer des données à partir du conteneur des propriétés

Toutes les données peuvent être récupérées à partir d’un package de données dans le gestionnaire de l' `Drop` événement, en accédant à la `Properties` collection du package de données.

La `DataPackageView` classe définit une `Properties` propriété, de type `DataPackagePropertySetView` . La `DataPackagePropertySetView` classe représente un conteneur de propriétés en lecture seule stocké en tant que `Dictionary<string, object>` .

L’exemple suivant montre un `Drop` Gestionnaire d’événements qui récupère des données à partir du jeu de propriétés d’un package de données pour un `Rectangle` :

```csharp
void OnDrop(object sender, DropEventArgs e)
{
    Square square = (Square)e.Data.Properties["Square"];

    // Perform logic to take action based on retrieved value.
}
```

Dans cet exemple, l' `Square` objet est récupéré à partir du conteneur des propriétés du package de données, en spécifiant la clé de dictionnaire « Square ». Une action basée sur la valeur récupérée peut alors être effectuée.

## <a name="related-links"></a>Liens connexes

- [Mouvement glisser-déplacer (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/WorkingWithGestures/DragAndDropGesture)
