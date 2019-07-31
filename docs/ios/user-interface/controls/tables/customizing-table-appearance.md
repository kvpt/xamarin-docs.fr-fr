---
title: Personnalisation de l’apparence d’un tableau dans Xamarin. iOS
description: Ce document explique comment personnaliser l’apparence d’un tableau dans Xamarin. iOS. Il décrit les styles de cellule, les accessoires, les séparateurs de cellule et les dispositions de cellules personnalisées.
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: cad7378e3d569454606a9d472cf30c42956553e4
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644943"
---
# <a name="customizing-a-tables-appearance-in-xamarinios"></a>Personnalisation de l’apparence d’un tableau dans Xamarin. iOS

Le moyen le plus simple de modifier l’apparence d’un tableau consiste à utiliser un style de cellule différent. Vous pouvez modifier le style de cellule qui est utilisé lors de la création `UITableViewSource`de `GetCell` chaque cellule dans la méthode de.

## <a name="cell-styles"></a>Styles de cellules

Il existe quatre styles intégrés:

-  **Default** : prend en `UIImageView`charge.
-  **Sous-titre** : `UIImageView` prend en charge un et un sous-titre.
-  **Valeur1** – sous-titre aligné à droite `UIImageView`, prend en charge un.
-  **Value2** : le titre est aligné à droite et le sous-titre est aligné à gauche (mais aucune image).


Ces captures d’écran montrent comment chaque style s’affiche:

 [![](customizing-table-appearance-images/image7.png "Ces captures d’écran montrent comment chaque style apparaît")](customizing-table-appearance-images/image7.png#lightbox)

L’exemple de **CellDefaultTable** contient le code permettant de créer ces écrans. Le style de cellule est défini dans `UITableViewCell` le constructeur, comme suit:

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

Les [Propriétés prises en charge](xref:UIKit.UITableViewCell) du style de cellule peuvent ensuite être définies:

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>Automobiles

Les accessoires peuvent être ajoutés à droite de l’affichage des cellules:

-   **Checkmark** : peut être utilisé pour indiquer la sélection multiple dans une table.
-   **DetailButton** : répond à Touch indépendamment du reste de la cellule, ce qui lui permet d’effectuer une fonction différente pour toucher la cellule elle-même (par exemple, ouvrir une fenêtre contextuelle ou une nouvelle fenêtre `UINavigationController` qui ne fait pas partie d’une pile).
-   **DisclosureIndicator** : normalement utilisé pour indiquer que le toucher à la cellule ouvre une autre vue.
-   **DetailDisclosureButton** : combinaison de `DetailButton` et `DisclosureIndicator`.


Voici à quoi elles ressemblent:

 [![](customizing-table-appearance-images/image8.png "Exemples d’accessoires")](customizing-table-appearance-images/image8.png#lightbox)

Pour afficher l’un de ces accessoires, vous pouvez `Accessory` définir la propriété `GetCell` dans la méthode:

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

`DetailButton` Lorsque ou `DetailDisclosureButton` sont affichés, vous devez également substituer la `AccessoryButtonTapped` pour effectuer une action quand elle est touchée.

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

L’exemple de **CellAccessoryTable** montre un exemple utilisant des accessoires.

## <a name="cell-separators"></a>Séparateurs de cellules

Les séparateurs de cellules sont des cellules de table utilisées pour séparer la table. Les propriétés sont définies sur la table.

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

Il est également possible d’ajouter un effet de flou ou de Vibrance au séparateur:

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

Le séparateur peut également avoir une incrustation:

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>Création de dispositions de cellules personnalisées

Pour modifier le style visuel d’une table, vous devez fournir des cellules personnalisées pour qu’elle s’affiche. La cellule personnalisée peut avoir des couleurs et des dispositions de contrôle différentes.

L’exemple CellCustomTable implémente une `UITableViewCell` sous-classe qui définit une disposition personnalisée `UILabel`de s et `UIImage` un avec différentes polices et couleurs. Les cellules résultantes se présentent comme suit:

 [![](customizing-table-appearance-images/image9.png "Dispositions de cellules personnalisées")](customizing-table-appearance-images/image9.png#lightbox)

La classe de cellule personnalisée se compose uniquement de trois méthodes:

-   **Constructor** : crée les contrôles d’interface utilisateur et définit les propriétés de style personnalisées (par exemple, type de police, taille et couleurs).
-   **UpdateCell** : méthode `UITableView.GetCell` à utiliser pour définir les propriétés de la cellule.
-   **LayoutSubviews** : définissez l’emplacement des contrôles de l’interface utilisateur. Dans l’exemple, chaque cellule a la même disposition, mais une cellule plus complexe (en particulier celles qui ont des tailles différentes) peut nécessiter des positions de disposition différentes selon le contenu affiché.


L’exemple de code complet dans **CellCustomTable > CustomVegeCell.cs** est le suivant:

```csharp
public class CustomVegeCell : UITableViewCell  {
    UILabel headingLabel, subheadingLabel;
    UIImageView imageView;
    public CustomVegeCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
    {
        SelectionStyle = UITableViewCellSelectionStyle.Gray;
        ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);
        imageView = new UIImageView();
        headingLabel = new UILabel () {
            Font = UIFont.FromName("Cochin-BoldItalic", 22f),
            TextColor = UIColor.FromRGB (127, 51, 0),
            BackgroundColor = UIColor.Clear
        };
        subheadingLabel = new UILabel () {
            Font = UIFont.FromName("AmericanTypewriter", 12f),
            TextColor = UIColor.FromRGB (38, 127, 0),
            TextAlignment = UITextAlignment.Center,
            BackgroundColor = UIColor.Clear
        };
        ContentView.AddSubviews(new UIView[] {headingLabel, subheadingLabel, imageView});

    }
    public void UpdateCell (string caption, string subtitle, UIImage image)
    {
        imageView.Image = image;
        headingLabel.Text = caption;
        subheadingLabel.Text = subtitle;
    }
    public override void LayoutSubviews ()
    {
        base.LayoutSubviews ();
        imageView.Frame = new CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
        headingLabel.Frame = new CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
        subheadingLabel.Frame = new CGRect (100, 18, 100, 20);
    }
}
```

La `GetCell` méthode`UITableViewSource` de doit être modifiée pour créer la cellule personnalisée:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (cellIdentifier) as CustomVegeCell;
    if (cell == null)
        cell = new CustomVegeCell (cellIdentifier);
    cell.UpdateCell (tableItems[indexPath.Row].Heading
            , tableItems[indexPath.Row].SubHeading
            , UIImage.FromFile ("Images/" + tableItems[indexPath.Row].ImageName) );
    return cell;
}
```



## <a name="related-links"></a>Liens associés

- [WorkingWithTables (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
