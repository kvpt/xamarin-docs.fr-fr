---
ms.openlocfilehash: e1705e9311d4875a5b866ac5acb7d20aa107a2bb
ms.sourcegitcommit: bc0c1740aa0708459729c0e671ab3ff7de3e2eee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83435435"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Grid`](xref:Xamarin.Forms.Grid) pour définir des colonnes et des lignes, et pour y placer du contenu :

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    Ce code définit des colonnes et lignes pour [`Grid`](xref:Xamarin.Forms.Grid)et positionne les instances [`Label`](xref:Xamarin.Forms.Label) dans des colonnes et des lignes spécifiques. Les données des colonnes et des lignes sont stockées dans les propriétés [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) et [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), qui représentent respectivement des collections d’objets [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) et [`RowDefinition`](xref:Xamarin.Forms.RowDefinition). La largeur de chaque `ColumnDefinition` est définie par la propriété [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) et la hauteur de chaque `RowDefinition` est définie par la propriété [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height). Les valeurs de largeur et de hauteur valides sont :

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto), qui dimensionne la colonne ou ligne en fonction du contenu.
    - Des valeurs proportionnelles qui dimensionnent les colonnes et les lignes proportionnellement à l’espace restant. Les valeurs proportionnelles se terminent par un `*`.
    - Des valeurs absolues qui dimensionnent les colonnes ou les lignes avec des valeurs fixes spécifiques.

    Par conséquent, dans le code ci-dessus, chaque colonne a une largeur correspondant à la moitié de [`Grid`](xref:Xamarin.Forms.Grid), tandis que chaque ligne a une hauteur de 50 unités indépendantes du périphérique.

    La position de chaque [`Label`](xref:Xamarin.Forms.Label) dans [`Grid`](xref:Xamarin.Forms.Grid) est spécifiée avec les propriétés [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) et [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) qui lui sont jointes, à l’aide d’un index de base zéro. La première colonne est donc la colonne 0 et la première ligne est la ligne 0. La première `Label` ne dispose pas de ces propriétés jointes, car n’importe quel affichage enfant qui ne la définit pas sera automatiquement restitué dans la colonne 0, ligne 0.

    > [!NOTE]
    > L’espacement entre les colonnes et lignes dans [`Grid`](xref:Xamarin.Forms.Grid) peut être défini avec les propriétés [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) et [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing). Pour plus d’informations, consultez la rubrique [Espacement](~/xamarin-forms/user-interface/layouts/grid.md#space-between-rows-and-columns) du guide [Grille de Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’une grille dont le contenu est réparti entre plusieurs colonnes et lignes, sur iOS et Android](../images/columns-rows.png "Grille comprenant du contenu s’étendant sur plusieurs colonnes et lignes")](../images/columns-rows-large.png#lightbox "Grille comprenant du contenu s’étendant sur plusieurs colonnes et lignes")

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Grid`](xref:Xamarin.Forms.Grid) pour définir des colonnes et des lignes, et pour y placer du contenu :

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    Ce code définit des colonnes et lignes pour [`Grid`](xref:Xamarin.Forms.Grid)et positionne les instances [`Label`](xref:Xamarin.Forms.Label) dans des colonnes et des lignes spécifiques. Les données des colonnes et des lignes sont stockées dans les propriétés [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) et [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), qui représentent respectivement des collections d’objets [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) et [`RowDefinition`](xref:Xamarin.Forms.RowDefinition). La largeur de chaque `ColumnDefinition` est définie par la propriété [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) et la hauteur de chaque `RowDefinition` est définie par la propriété [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height). Les valeurs de largeur et de hauteur valides sont :

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto), qui dimensionne la colonne ou ligne en fonction du contenu.
    - Des valeurs proportionnelles qui dimensionnent les colonnes et les lignes proportionnellement à l’espace restant. Les valeurs proportionnelles se terminent par un `*`.
    - Des valeurs absolues qui dimensionnent les colonnes ou les lignes avec des valeurs fixes spécifiques.

    Par conséquent, dans le code ci-dessus, chaque colonne a une largeur correspondant à la moitié de [`Grid`](xref:Xamarin.Forms.Grid), tandis que chaque ligne a une hauteur de 50 unités indépendantes du périphérique.

    La position de chaque [`Label`](xref:Xamarin.Forms.Label) dans [`Grid`](xref:Xamarin.Forms.Grid) est spécifiée avec les propriétés [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) et [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) qui lui sont jointes, à l’aide d’un index de base zéro. La première colonne est donc la colonne 0 et la première ligne est la ligne 0. La première `Label` ne dispose pas de ces propriétés jointes, car n’importe quel affichage enfant qui ne la définit pas sera automatiquement restitué dans la colonne 0, ligne 0.

    > [!NOTE]
    > L’espacement entre les colonnes et lignes dans [`Grid`](xref:Xamarin.Forms.Grid) peut être défini avec les propriétés [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) et [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing). Pour plus d’informations, consultez la rubrique [Espacement](~/xamarin-forms/user-interface/layouts/grid.md#space-between-rows-and-columns) du guide [Grille de Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une grille dont le contenu est réparti entre plusieurs colonnes et lignes, sur iOS et Android](../images/columns-rows.png "Grille comprenant du contenu s’étendant sur plusieurs colonnes et lignes")](../images/columns-rows-large.png#lightbox "Grille comprenant du contenu s’étendant sur plusieurs colonnes et lignes")
