---
titre : « Xamarin.Forms ListView » Description : « ce guide présente le Xamarin.Forms ListView, qui peut être utilisé pour présenter des données dans des listes interactives ».
ms. Prod : xamarin ms. AssetID : FEFDF7E0-720F-4BD1-863F-4477226AA695 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 09/04/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-listview"></a>Xamarin.FormsListe

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)est une vue pour la présentation de listes de données, en particulier les longues listes nécessitant un défilement.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)est une vue pour la présentation de listes de données à l’aide de différentes spécifications de disposition. Il vise à fournir une alternative plus flexible et plus performante à [`ListView`](xref:Xamarin.Forms.ListView) . Pour plus d’informations, consultez [ Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Cas d'utilisation

Un `ListView` contrôle peut être utilisé dans toutes les situations où vous affichez des listes de données avec défilement. La `ListView` classe prend en charge les actions de contexte et la liaison de données.

Le `ListView` contrôle ne doit pas être confondu avec le [`TableView`](~/xamarin-forms/user-interface/tableview.md) contrôle. Le `TableView` contrôle est une meilleure option chaque fois que vous disposez d’une liste non liée d’options ou de données, car elle permet de spécifier des options prédéfinies en XAML. Par exemple, l’application paramètres iOS, qui possède un ensemble d’options prédéfinies, est mieux adaptée à l’utilisation d’un `TableView` `ListView` .

La `ListView` classe ne prend pas en charge la définition d’éléments de liste en XAML, vous devez utiliser la `ItemsSource` propriété ou la liaison de données avec un `ItemTemplate` pour définir des éléments dans la liste.

Un `ListView` est mieux adapté à une collection composée d’un type de données unique. Cette exigence est due au fait qu’un seul type de cellule peut être utilisé pour chaque ligne de la liste. Le `TableView` contrôle peut prendre en charge plusieurs types de cellules. il s’agit donc d’une meilleure option lorsque vous devez afficher plusieurs types de données.

Pour plus d’informations sur la liaison de données à une `ListView` instance, consultez [sources de données ListView](~/xamarin-forms/user-interface/listview/data-and-databinding.md).

## <a name="components"></a>Composants

Le `ListView` contrôle a un certain nombre de composants disponibles pour tester les fonctionnalités natives de chaque plateforme. Ces composants sont définis dans les sections suivantes.

### <a name="headers-and-footers"></a>[En-têtes et pieds de page](customizing-list-appearance.md#headers-and-footers)

Les composants d’en-tête et de pied de page s’affichent au début et à la fin d’une liste, séparés des données de la liste. Les en-têtes et les pieds de page peuvent être liés à une source de données distincte de la source de données de ListView.

### <a name="groups"></a>[Groupes](customizing-list-appearance.md#grouping)

Les données d’un `ListView` peuvent être regroupées pour faciliter la navigation. Les groupes sont généralement liés aux données. La capture d’écran suivante montre un `ListView` avec des données groupées :

[![« Données groupées dans un ListView »](images/grouping-depth-cropped.png)](images/grouping-depth.png#lightbox "Données groupées dans un ListView")

### <a name="cells"></a>[Cellules](customizing-cell-appearance.md)

Les éléments de données dans un `ListView` sont appelés des cellules. Chaque cellule correspond à une ligne de données. Vous avez le choix entre des cellules intégrées, ou vous pouvez définir votre propre cellule personnalisée. Les cellules intégrées et personnalisées peuvent être utilisées/définies en XAML ou dans du code.

- Les [cellules intégrées](customizing-cell-appearance.md#built-in-cells), telles que `TextCell` et `ImageCell` , correspondent aux contrôles natifs et sont particulièrement performants.
  - Un [`TextCell`](customizing-cell-appearance.md#textcell) affiche une chaîne de texte, éventuellement avec un texte de détail. Le texte de détail est rendu sous la forme d’une deuxième ligne dans une police plus petite avec une couleur d’accentuation.
  - Un [`ImageCell`](customizing-cell-appearance.md#imagecell) affiche une image avec du texte. Apparaît en tant que `TextCell` avec une image sur la gauche.
- Les [cellules personnalisées](customizing-cell-appearance.md#custom-cells) sont utilisées pour présenter des données complexes. Par exemple, une cellule personnalisée peut être utilisée pour présenter une liste de chansons incluant l’album et l’artiste.

La capture d’écran suivante montre un `ListView` avec des éléments ImageCell :

[![« ImageCell les éléments dans un ListView »](images/image-cell-default-cropped.png)](images/image-cell-default.png#lightbox "ImageCell des éléments dans un ListView")

Pour en savoir plus sur la personnalisation des cellules dans un `ListView` , consultez Personnalisation de l' [apparence des cellules ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Fonctionnalités

La `ListView` classe prend en charge un certain nombre de styles d’interaction.

- L' [extraction à l’actualisation](interactivity.md#pull-to-refresh) permet à l’utilisateur d’extraire le `ListView` contenu pour actualiser le contenu.
- Les [actions de contexte](interactivity.md#context-actions) permettent au développeur de spécifier des actions personnalisées sur des éléments de liste individuels. Par exemple, vous pouvez implémenter une opération d’interrogation sur iOS ou des actions à long terme sur Android.
- [Sélection](interactivity.md#selection-and-taps) permet au développeur d’associer des fonctionnalités à des événements de sélection et de désélection sur des éléments de liste.

La capture d’écran suivante montre une `ListView` avec des actions de contexte :

[![« Actions de contexte dans un ListView »](images/context-default-cropped.png)](images/context-default.png#lightbox "Actions de contexte dans un ListView")

Pour en savoir plus sur les fonctionnalités d’interactivité de `ListView` , consultez [actions & interactivité avec ListView](interactivity.md).

## <a name="related-links"></a>Liens connexes

- [Utilisation de ListView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [Liaison bidirectionnelle (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [Cellules intégrées (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Cellules personnalisées (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Regroupement (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Vue du convertisseur personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [Interactivité de ListView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
