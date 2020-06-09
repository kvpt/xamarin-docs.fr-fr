---
titre : « Xamarin.Forms liaisons compilées » Description : « cet article explique comment utiliser des liaisons compilées pour améliorer les performances de liaison de données dans les Xamarin.Forms applications. »
ms. Prod : xamarin ms. AssetID : ABE6B7F7-875E-4402-A1D2-845CE374402B ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 09/18/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-compiled-bindings"></a>Xamarin.FormsLiaisons compilées

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_Les liaisons compilées sont résolues plus rapidement que les liaisons classiques, ce qui améliore les performances de liaison de données dans les Xamarin.Forms applications._

Les liaisons de données présentent deux problèmes principaux :

1. Aucune validation des expressions de liaison n’a lieu au moment de la compilation. À la place, les liaisons sont résolues au moment de l’exécution. Par conséquent, les liaisons non valides ne sont pas détectées avant l’exécution, lorsque l’application ne se comporte pas comme prévu ou que des messages d’erreur apparaissent.
1. Elles ne sont pas rentables. Les liaisons sont résolues au moment de l’exécution via l’inspection (réflexion) des objets à usage général et la surcharge de cette opération varie d’une plateforme à l’autre.

Les liaisons compilées améliorent les performances de liaison de données dans les Xamarin.Forms applications en résolvant les expressions de liaison au moment de la compilation plutôt qu’au moment de l’exécution. En outre, cette validation des expressions de liaison au moment de la compilation favorise une meilleure expérience de dépannage pour les développeurs, car les liaisons non valides sont signalées comme erreurs de build.

Le processus permettant d’utiliser des liaisons compilées est le suivant :

1. Activez la compilation XAML. Pour plus d’informations sur la compilation XAML, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).
1. Affectez à un `x:DataType` attribut sur [`VisualElement`](xref:Xamarin.Forms.VisualElement) le type de l’objet auquel le `VisualElement` et ses enfants seront liés.

> [!NOTE]
> Il est recommandé de définir l' `x:DataType` attribut au même niveau dans la hiérarchie d’affichage lorsque le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) est défini. Toutefois, cet attribut peut être redéfini à n’importe quel emplacement d’une hiérarchie d’affichage.

Pour utiliser des liaisons compilées, l' `x:DataType` attribut doit être défini sur un littéral de chaîne ou un type à l’aide de l' `x:Type` extension de balisage. Au moment de la compilation XAML, toutes les expressions de liaison non valides seront signalées comme erreurs de build. Toutefois, le compilateur XAML signalera uniquement une erreur de build pour la première expression de liaison non valide qu’il rencontrera. Toutes les expressions de liaison valides définies sur le `VisualElement` ou ses enfants seront compilées, que le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) soit défini en XAML ou dans le code. La compilation d’une expression de liaison génère du code compilé qui obtiendra une valeur d’une propriété de la *source* et la définira sur la propriété de la *cible* spécifiée dans le balisage. En outre, en fonction de l’expression de liaison, le code généré peut observer des modifications dans la valeur de la propriété *source* et actualiser la propriété *cible*, et il peut renvoyer (push) des modifications à partir de la *cible* vers la *source*.

> [!IMPORTANT]
> Les liaisons compilées sont actuellement désactivées pour toutes les expressions de liaison qui définissent la [`Source`](xref:Xamarin.Forms.Binding.Source) propriété. Cela tient au fait que la propriété `Source` est toujours définie à l’aide de l’extension de balisage `x:Reference`, qui ne peut pas être résolue au moment de la compilation.

## <a name="use-compiled-bindings"></a>Utiliser des liaisons compilées

La page du **Sélecteur de couleurs compilées** montre l’utilisation de liaisons compilées entre Xamarin.Forms les vues et les propriétés ViewModel :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />
            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>    
</ContentPage>
```

La racine [`StackLayout`](xref:Xamarin.Forms.StackLayout) instancie `HslColorViewModel` et initialise la `Color` propriété dans les balises d’éléments de propriété pour la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriété. Cette racine `StackLayout` définit également l' `x:DataType` attribut comme type ViewModel, indiquant que toutes les expressions de liaison dans la `StackLayout` hiérarchie d’affichage racine seront compilées. Cela peut être vérifié en modifiant l’une des expressions de liaison à lier à une propriété ViewModel inexistante, ce qui provoque une erreur de génération. Bien que cet exemple affecte `x:DataType` un littéral de chaîne à l’attribut, il peut également être défini sur un type avec l' `x:Type` extension de balisage. Pour plus d’informations sur l' `x:Type` extension de balisage, consultez [X :type, extension de balisage](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension).

> [!IMPORTANT]
> L’attribut `x:DataType` peut être redéfini à tout emplacement dans une hiérarchie d’affichage.

Les [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) éléments, et les [`Slider`](xref:Xamarin.Forms.Slider) vues héritent du contexte de liaison du [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Ces vues sont toutes des cibles de liaison qui référencent des propriétés de source dans le ViewModel. Pour la [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) propriété, et la [`Label.Text`](xref:Xamarin.Forms.Label.Text) propriété, les liaisons de données sont `OneWay` : les propriétés de la vue sont définies à partir des propriétés dans le ViewModel. Toutefois, la [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propriété utilise une `TwoWay` liaison. Cela permet de `Slider` définir chaque à partir du ViewModel, et également de définir le ViewModel à partir de chaque `Slider` .

Lorsque l’application est exécutée pour la première fois, les [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) éléments, et [`Slider`](xref:Xamarin.Forms.Slider) sont tous définis à partir du ViewModel en fonction du jeu de propriétés initial lors de l' `Color` instanciation du ViewModel. Ceci est illustré dans les captures d’écran suivantes :

[![Sélecteur de couleurs compilées](compiled-bindings-images/compiledcolorselector-small.png "Sélecteur de couleurs compilées")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Sélecteur de couleurs compilées")

À mesure que les curseurs sont manipulés, les [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) éléments et sont mis à jour en conséquence.

Pour plus d’informations sur ce sélecteur de couleur, consultez [ViewModels et notifications de modification de propriété](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## <a name="use-compiled-bindings-in-a-datatemplate"></a>Utiliser des liaisons compilées dans un DataTemplate

Les liaisons dans un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) sont interprétées dans le contexte de l’objet qui est basé sur un modèle. Par conséquent, lorsque vous utilisez des liaisons compilées dans un `DataTemplate`, le `DataTemplate` doit déclarer le type de son objet de données à l’aide de l’attribut `x:DataType`.

La page **Compiled Color List** (Liste de couleurs compilée) illustre l’utilisation des liaisons compilées dans un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

La propriété [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) a la valeur de la `NamedColor.All` propriété statique. La `NamedColor` classe utilise la réflexion .net pour énumérer tous les champs publics statiques dans la [`Color`](xref:Xamarin.Forms.Color) structure et les stocker avec leurs noms dans une collection accessible à partir de la `All` propriété statique. Par conséquent, l’objet `ListView` est rempli avec toutes les instances de `NamedColor`. Pour chaque élément figurant dans `ListView`, le contexte de liaison de l’élément est défini sur un objet `NamedColor`. Les [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) éléments et dans le [`ViewCell`](xref:Xamarin.Forms.ViewCell) sont liés aux `NamedColor` Propriétés.

Notez que [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définit l' `x:DataType` attribut comme étant le `NamedColor` type, ce qui indique que toutes les expressions de liaison dans la `DataTemplate` hiérarchie de vue seront compilées. Cela peut être vérifié en modifiant les expressions de liaison à lier à une propriété de `NamedColor` inexistante, ce qui entraînera une erreur de build.  Bien que cet exemple affecte `x:DataType` un littéral de chaîne à l’attribut, il peut également être défini sur un type avec l' `x:Type` extension de balisage. Pour plus d’informations sur l' `x:Type` extension de balisage, consultez [X :type, extension de balisage](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension).

Lorsque l’application est exécutée pour la première fois, la [`ListView`](xref:Xamarin.Forms.ListView) est remplie avec des `NamedColor` instances de. Lorsqu’un élément de `ListView` est sélectionné, la [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) propriété est définie sur la couleur de l’élément sélectionné dans le `ListView` :

[![Liste des couleurs compilées](compiled-bindings-images/compiledcolorlist-small.png "Liste de couleurs compilée]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Liste des couleurs compilées")

La sélection d’autres éléments dans [`ListView`](xref:Xamarin.Forms.BoxView) met à jour la couleur de [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="combine-compiled-bindings-with-classic-bindings"></a>Combiner des liaisons compilées avec des liaisons classiques

Les expressions de liaison sont compilées uniquement pour la hiérarchie d’affichage sur laquelle l’attribut `x:DataType` est défini. À l’inverse, toutes les vues dans une hiérarchie sur laquelle l’attribut `x:DataType` n’est pas défini utiliseront des liaisons classiques. Par conséquent, il est possible de combiner des liaisons compilées et classiques sur une page. Par exemple, dans la section précédente, les vues dans [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) utilisent des liaisons compilées, alors que la [`BoxView`](xref:Xamarin.Forms.BoxView) valeur définie pour la couleur sélectionnée dans le [`ListView`](xref:Xamarin.Forms.ListView) ne l’est pas.

La structuration prudente des attributs `x:DataType` permet ainsi d’obtenir une page utilisant des liaisons compilées et classiques. Vous pouvez également redéfinir l’attribut `x:DataType` à tout moment dans une hiérarchie d’affichage sur `null` à l’aide de l’extension de balisage `x:Null`. Cela indique que toutes les expressions de liaison figurant dans la hiérarchie d’affichage utiliseront des liaisons classiques. La page *Mixed Bindings* (Liaisons mixtes) illustre cette approche :

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

La racine [`StackLayout`](xref:Xamarin.Forms.StackLayout) affecte au `x:DataType` type de l’attribut `HslColorViewModel` , ce qui indique que toute expression de liaison dans la `StackLayout` hiérarchie d’affichage racine sera compilée. Toutefois, le `StackLayout` interne redéfinit l’attribut `x:DataType` sur `null` avec l’expression de balisage `x:Null`. Par conséquent, les expressions de liaison figurant dans le `StackLayout` interne utilisent des liaisons classiques. Seul le [`BoxView`](xref:Xamarin.Forms.BoxView) , au sein de la `StackLayout` hiérarchie d’affichage racine, utilise des liaisons compilées.

Pour plus d'informations sur l’expression de balisage `x:Null`, consultez [Extension de balisage x:Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#xnull-markup-extension).

## <a name="performance"></a>Performances

Les liaisons compilées améliorent de façon variable les performances de liaison de données. Les tests unitaires révèlent que :

- Une liaison compilée qui utilise la notification de modification de propriété (c'est-à-dire une liaison `OneWay`, `OneWayToSource` ou `TwoWay`) est résolue environ 8 fois plus vite qu’une liaison classique.
- Une liaison compilée qui n’utilise pas la notification de modification de propriété (c'est-à-dire une liaison `OneTime`) est résolue environ 20 fois plus vite qu’une liaison classique.
- La définition [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de sur une liaison compilée qui utilise la notification de modification de propriété (c’est-à-dire une `OneWay` `OneWayToSource` liaison, ou `TwoWay` ) est environ 5 fois plus rapide que la définition `BindingContext` de sur une liaison classique.
- La définition [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de sur une liaison compilée qui n’utilise pas la notification de modification de propriété (c’est-à-dire une `OneTime` liaison) est environ 7 fois plus rapide que la définition `BindingContext` de sur une liaison classique.

Ces différences de performances peuvent être accrues sur les appareils mobiles, selon la plateforme utilisée, la version du système d’exploitation utilisé et l’appareil sur lequel l’application s’exécute.

## <a name="related-links"></a>Liens connexes

- [Démonstrations de liaison de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
