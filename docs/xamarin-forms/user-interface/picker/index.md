---
titre : « Xamarin.Forms Sélecteur » Description : «le Xamarin.Forms Sélecteur affiche une liste succincte d’éléments, à partir de laquelle l’utilisateur peut sélectionner un élément. Cet article explique comment utiliser la classe Picker pour sélectionner un élément de texte dans une liste de données.
ms. Prod : xamarin ms. AssetID : D4815A4B-104B-4294-951B-BD8F2EC33C86 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 02/26/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-picker"></a>Xamarin.FormsEmoji

_La vue sélecteur est un contrôle permettant de sélectionner un élément de texte dans une liste de données._

Xamarin.Forms [`Picker`](xref:Xamarin.Forms.Picker) Affiche une brève liste d’éléments, à partir de laquelle l’utilisateur peut sélectionner un élément. `Picker` définit les propriétés suivantes :

- [`Title`](xref:Xamarin.Forms.Picker.Title)de type `string` , qui a comme valeur par défaut `null` .
- `TitleColor`de type [`Color`](xref:Xamarin.Forms.Color) , la couleur utilisée pour afficher le `Title` texte.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource)de type `IList` , la liste source des éléments à afficher, dont la valeur par défaut est `null` .
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)de type `int` , l’index de l’élément sélectionné, qui a comme valeur par défaut-1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)de type `object` , l’élément sélectionné, qui a comme valeur par défaut `null` .
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor)de type [`Color`](xref:Xamarin.Forms.Color) , la couleur utilisée pour afficher le texte, qui a comme valeur par défaut [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes)de type [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , qui a comme valeur par défaut [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily)de type `string` , qui a comme valeur par défaut `null` .
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize)de type `double` , qui a comme valeur par défaut-1,0.
- `CharacterSpacing`, de type `double` , est l’espacement entre les caractères de l’élément affiché par le `Picker` .

Toutes les propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être stylisées et que les propriétés peuvent être des cibles de liaisons de données. Les [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) Propriétés et ont un mode de liaison par défaut de, ce qui [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) signifie qu’elles peuvent être des cibles de liaisons de données dans une application qui utilise l’architecture [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) . Pour plus d’informations sur la définition des propriétés de police, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

Un [`Picker`](xref:Xamarin.Forms.Picker) n’affiche aucune donnée lorsqu’il est affiché pour la première fois. Au lieu de cela, la valeur de sa [`Title`](xref:Xamarin.Forms.Picker.Title) propriété est affichée sous la forme d’un espace réservé sur les plateformes iOS et Android :

[![](images/picker-initial.png "Initial Picker Display")](images/picker-initial-large.png#lightbox "Initial Picker Display")

Lorsque le [`Picker`](xref:Xamarin.Forms.Picker) obtient le focus, ses données sont affichées et l’utilisateur peut sélectionner un élément :

[![](images/picker-selection.png "Picker Selecting an Item")](images/picker-selection-large.png#lightbox "Picker Selecting an Item")

Le [`Picker`](xref:Xamarin.Forms.Picker) déclenche un [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) événement lorsque l’utilisateur sélectionne un élément. Après la sélection, l’élément sélectionné est affiché par le `Picker` :

![](images/picker-after-selection.png "Picker after Selection")

Il existe deux techniques pour remplir un [`Picker`](xref:Xamarin.Forms.Picker) avec des données :

- Définition de la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propriété sur les données à afficher. Il s’agit de la technique recommandée. Pour plus d’informations, consultez [définition de la propriété ItemsSource d’un sélecteur](populating-itemssource.md).
- Ajout des données à afficher dans la [`Items`](xref:Xamarin.Forms.Picker.Items) collection. Cette technique était le processus d’origine pour le remplissage d’un [`Picker`](xref:Xamarin.Forms.Picker) avec des données. Pour plus d’informations, consultez [Ajout de données à la collection d’éléments d’un sélecteur](populating-items.md).

## <a name="related-links"></a>Liens connexes

- [Sélecteur](xref:Xamarin.Forms.Picker)
