---
title: Contrôles de table Watchos dans Xamarin
description: Ce document explique comment utiliser les contrôles de table Watchos dans Xamarin. Il aborde l’ajout d’une table, l’ajout d’un contrôleur de ligne, la création et le remplissage de lignes, la réponse aux pressions, etc.
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 015f0732e4d8cdf771af3e1d0b3cc3e31b6e806c
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572255"
---
# <a name="watchos-table-controls-in-xamarin"></a>Contrôles de table Watchos dans Xamarin

Le contrôle Watchos `WKInterfaceTable` est bien plus simple que son équivalent iOS, mais il joue un rôle similaire. Il crée une liste déroulante de lignes qui peuvent avoir des dispositions personnalisées et qui répondent aux événements tactiles.

![](table-images/table-list-sml.png "Liste des tables de surveillance") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>Ajout d’une table

Faites glisser le contrôle **table** dans une scène. Par défaut, elle ressemble à ceci (avec une disposition de ligne non spécifiée unique) :

[![](table-images/add-table-sml.png "Adding a table")](table-images/add-table.png#lightbox)

Donnez un nom à la table dans la zone **nom** du panneau **Propriétés** , afin qu’elle puisse être référencée dans le code.

## <a name="add-a-row-controller"></a>Ajouter un contrôleur de ligne

La table inclut automatiquement une seule ligne, représentée par un contrôleur de ligne qui contient un contrôle de **groupe** par défaut.

Pour définir la **classe** du contrôleur de ligne, sélectionnez la ligne dans la **structure du document** et tapez un nom de classe dans le panneau **Propriétés** :

[![](table-images/add-row-controller-sml.png "Entering a class name in the Properties pad")](table-images/add-row-controller.png#lightbox)

Une fois que la classe du contrôleur de la ligne est définie, l’IDE crée un fichier C# correspondant dans le projet. Faites glisser des contrôles (tels que des étiquettes) sur la ligne et attribuez-leur des noms pour qu’ils puissent être référencés dans le code.

## <a name="create-and-populate-rows"></a>Créer et remplir des lignes

`SetNumberOfRows`crée les classes de contrôleur de ligne pour chaque ligne, à l’aide de `Identifier` pour sélectionner le bon. Si vous avez donné un personnalisé à votre contrôleur de ligne `Identifier` , remplacez la **valeur par défaut** dans l’extrait de code ci-dessous par l’identificateur que vous avez utilisé. Le `RowController` *de chaque ligne* est créé lorsque `SetNumberOfRows` est appelé et que la table est affichée.

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
    // loads row controller by identifier
```

> [!IMPORTANT]
> Les lignes de table ne sont pas virtualisées comme elles le sont dans iOS. Essayez de limiter le nombre de lignes (la commande Apple recommande moins de 20).

Une fois les lignes créées, vous devez remplir chaque cellule (comme `GetCell` dans IOS). Cet extrait de code de l' [exemple WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables) met à jour l’étiquette dans chaque ligne

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> L’utilisation de `SetNumberOfRows` , puis la boucle à l’aide de `GetRowController` entraîne l’envoi de la table entière à la montre. Dans les vues suivantes de la table, si vous devez ajouter ou supprimer des lignes spécifiques, utilisez `InsertRowsAt` et `RemoveRowsAt` pour obtenir de meilleures performances.

## <a name="respond-to-taps"></a>Répondre aux pressions

Vous pouvez répondre à la sélection de lignes de deux manières différentes :

- Implémentez la `DidSelectRow` méthode sur le contrôleur d’interface, ou
- Créez un segue sur la table de montage séquentiel et implémentez `GetContextForSegue` si vous souhaitez que la sélection de ligne ouvre une autre scène.

### <a name="didselectrow"></a>DidSelectRow

Pour gérer la sélection de lignes par programmation, implémentez la `DidSelectRow` méthode. Pour ouvrir une nouvelle scène, utilisez `PushController` et transmettez l’identificateur de la scène et le contexte de données à utiliser :

```csharp
public override void DidSelectRow (WKInterfaceTable table, nint rowIndex)
{
    var rowData = rows [(int)rowIndex];
    Console.WriteLine ("Row selected:" + rowData);
    // if selection should open a new scene
    PushController ("secondInterface", rows[(int)rowIndex]);
}
```

### <a name="getcontextforsegue"></a>GetContextForSegue

Faites glisser un segue sur le Storyboard de la ligne de votre table vers une autre scène (maintenez la touche **CTRL** enfoncée tout en faisant glisser).
Veillez à sélectionner segue et donnez-lui un identificateur dans le panneau **Propriétés** (comme `secondLevel` dans l’exemple ci-dessous).

Dans le contrôleur d’interface, implémentez la `GetContextForSegue` méthode et retournez le contexte de données qui doit être fourni à la scène présentée par le segue.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

Ces données sont passées à la scène de Storyboard cible dans sa `Awake` méthode.

## <a name="multiple-row-types"></a>Plusieurs types de lignes

Par défaut, le contrôle de table a un seul type de ligne que vous pouvez concevoir. Pour ajouter plus de lignes « modèles », utilisez la zone **lignes** dans le panneau **Propriétés** pour créer d’autres contrôleurs de ligne :

![](table-images/prototype-rows1.png "Setting the number of Prototype rows")

Si vous affectez à la propriété **Rows** la valeur **3** , des espaces réservés de ligne supplémentaires sont créés pour vous faire glisser des contrôles. Pour chaque ligne, définissez le nom de la **classe** dans le panneau **Propriétés** pour vous assurer que la classe du contrôleur de ligne est créée.

![](table-images/prototype-rows2.png "The prototype rows in the designer")

Pour remplir une table avec des types de ligne différents `SetRowTypes` , utilisez la méthode pour spécifier le type de contrôleur de ligne à utiliser pour chaque ligne de la table. Utilisez les identificateurs de la ligne pour spécifier le contrôleur de lignes à utiliser pour chaque ligne.

Le nombre d’éléments de ce tableau doit correspondre au nombre de lignes attendues dans la table :

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

Lors du remplissage d’une table avec plusieurs contrôleurs de ligne, vous devez effectuer le suivi du type que vous attendez quand vous remplissez l’interface utilisateur :

```csharp
for (var i = 0; i < rows.Count; i++) {
    if (i == 0) {
        var elementRow = (Type1RowController)myTable.GetRowController (i);
        // populate UI controls
    } else if (i == 3) {
        var elementRow = (Type2RowController)myTable.GetRowController (i);
        // populate UI controls
    } else {
        var elementRow = (DefaultRowController)myTable.GetRowController (i);
        // populate UI controls
    }
}
```

## <a name="vertical-detail-paging"></a>Pagination des détails verticale

Watchos 3 a introduit une nouvelle fonctionnalité pour les tables : la possibilité de faire défiler les pages de détails associées à chaque ligne, sans avoir à revenir à la table et à choisir une autre ligne. Vous pouvez faire défiler les écrans de détails en effectuant un balayage vers le haut ou vers le haut, ou en utilisant le Digital Crown.

![](table-images/table-scroll-sml.png "Exemple de pagination des détails verticaux") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> Cette fonctionnalité n’est actuellement disponible qu’en modifiant le Storyboard dans Xcode Interface Builder.

Pour activer cette fonctionnalité, sélectionnez `WKInterfaceTable` dans l’aire de conception et cochez l’option de **pagination détails verticaux** :

![](table-images/vertical-detail-paging-sml.png "Selecting the Vertical Detail Paging option")

Comme [expliqué par Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) , la navigation dans les tables doit utiliser SEGUES pour que la fonctionnalité de pagination fonctionne. Réécrivez le code existant qui utilise `PushController` pour utiliser SEGUES à la place.

<a name="add_row_controller"></a>

## <a name="appendix-row-controller-code-example"></a>Annexe : exemple de code de contrôleur de ligne

L’IDE créera automatiquement deux fichiers de code lorsqu’un contrôleur de lignes sera créé dans le concepteur. Le code de ces fichiers générés est indiqué ci-dessous pour référence.

Le premier sera nommé pour la classe, par exemple **RowController.cs**, comme suit :

```csharp
using System;
using Foundation;

namespace WatchTablesExtension
{
    public partial class RowController : NSObject
    {
        public RowController ()
        {
        }
    }
}
```

L’autre fichier **. Designer.cs** est une définition de classe partielle qui contient les prises et les actions créées sur l’aire du concepteur, comme dans cet exemple avec un `WKInterfaceLabel` contrôle :

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using UIKit;

namespace WatchTables.OnWatchExtension
{
    [Register ("RowController")]
    partial class RowController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        public WatchKit.WKInterfaceLabel MyLabel { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (MyLabel != null) {
                MyLabel.Dispose ();
                MyLabel = null;
            }
        }
    }
}
```

Les prises et les actions déclarées ici peuvent ensuite être référencées dans le code. Toutefois, le fichier **. Designer.cs** ne doit pas être modifié directement.

## <a name="related-links"></a>Liens connexes

- [WatchTables (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
- [WatchKitCatalog (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Document de la table Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable)
