---
titre : «partie 4. Concepts de base de la liaison de données « Description : » les liaisons de données permettent de lier les propriétés de deux objets afin qu’une modification dans l’un provoque une modification de l’autre.»
ms. Prod : xamarin ms. Technology : xamarin-Forms ms. AssetID : 342288C3-BB4C-4924-B178-72E112D777BA auteur : davidbritch ms. Author : dabritch ms. Date : 10/25/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="part-4-data-binding-basics"></a>Partie 4. Notions de base sur la liaison de données

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Les liaisons de données permettent de lier les propriétés de deux objets afin qu’une modification dans l’un provoque une modification dans l’autre. Il s’agit d’un outil très utile, tandis que les liaisons de données peuvent être définies entièrement dans le code, XAML fournit des raccourcis et une commodité. Par conséquent, l’une des extensions de balisage les plus importantes dans Xamarin.Forms est la liaison._

## <a name="data-bindings"></a>Liaisons de données

Les liaisons de données connectent les propriétés de deux objets, appelés la *source* et la *cible*. Dans le code, deux étapes sont requises : la `BindingContext` propriété de l’objet cible doit être définie sur l’objet source, et la `SetBinding` méthode (souvent utilisée conjointement avec la `Binding` classe) doit être appelée sur l’objet cible pour lier une propriété de cet objet à une propriété de l’objet source.

La propriété cible doit être une propriété pouvant être liée, ce qui signifie que l’objet cible doit dériver de `BindableObject` . La documentation en ligne Xamarin.Forms indique les propriétés pouvant être liées. Une propriété de `Label` telle que `Text` est associée à la propriété pouvant être liée `TextProperty` .

Dans le balisage, vous devez également effectuer les deux mêmes étapes que celles requises dans le code, sauf que l' `Binding` extension de balisage prend la place de l' `SetBinding` appel et de la `Binding` classe.

Toutefois, lorsque vous définissez des liaisons de données en XAML, il existe plusieurs façons de définir le `BindingContext` de l’objet cible. Elle est parfois définie à partir du fichier code-behind, parfois à l’aide d’une `StaticResource` `x:Static` extension de balisage ou, et parfois en tant que contenu de `BindingContext` balises d’éléments de propriété.

Les liaisons sont utilisées le plus souvent pour connecter les éléments visuels d’un programme à un modèle de données sous-jacent, généralement dans une réalisation de l’architecture d’application MVVM (Model-View-ViewModel), comme indiqué dans la [partie 5. Des liaisons de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), mais d’autres scénarios sont possibles.

## <a name="view-to-view-bindings"></a>Liaisons de vue à vue

Vous pouvez définir des liaisons de données pour lier les propriétés de deux affichages sur la même page. Dans ce cas, vous définissez le `BindingContext` de l’objet cible à l’aide de l' `x:Reference` extension de balisage.

Voici un fichier XAML qui contient un `Slider` et deux `Label` vues, dont l’une est pivotée par la `Slider` valeur et une autre qui affiche la `Slider` valeur :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">

    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Le `Slider` contient un `x:Name` attribut qui est référencé par les deux `Label` vues à l’aide de l' `x:Reference` extension de balisage.

L' `x:Reference` extension de liaison définit une propriété nommée `Name` pour définir sur le nom de l’élément référencé, dans ce cas `slider` . Toutefois, la `ReferenceExtension` classe qui définit l' `x:Reference` extension de balisage définit également un `ContentProperty` attribut pour, ce qui `Name` signifie qu’elle n’est pas explicitement requise. Pour la variété, la première `x:Reference` comprend « Name = », mais pas la seconde :

```xaml
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

L' `Binding` extension de balisage elle-même peut avoir plusieurs propriétés, tout comme la `BindingBase` `Binding` classe et. `ContentProperty`Pour `Binding` est `Path` , mais la partie « Path = » de l’extension de balisage peut être omise si le chemin d’accès est le premier élément de l’extension de `Binding` balisage. Le premier exemple a « Path = », mais le deuxième exemple l’omet :

```xaml
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Les propriétés peuvent toutes être sur une seule ligne ou être séparées en plusieurs lignes :

```xaml
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Faites tout ce qui est pratique.

Notez la `StringFormat` propriété dans la deuxième `Binding` extension de balisage. Dans Xamarin.Forms , les liaisons n’effectuent pas de conversions de types implicites, et si vous devez afficher un objet non-chaîne en tant que chaîne, vous devez fournir un convertisseur de type ou utiliser `StringFormat` . En arrière-plan, la `String.Format` méthode statique est utilisée pour implémenter `StringFormat` . Il s’agit probablement d’un problème, car les spécifications de mise en forme .NET impliquent des accolades, qui sont également utilisées pour délimiter des extensions de balisage. Cela crée un risque de déroutant de l’analyseur XAML. Pour éviter cela, placez l’intégralité de la chaîne de mise en forme entre des guillemets simples :

```xaml
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Voici le programme en cours d’exécution :

[![Liaisons de vue à vue](data-binding-basics-images/sliderbinding.png)](data-binding-basics-images/sliderbinding-large.png#lightbox)

## <a name="the-binding-mode"></a>Mode de liaison

Une vue unique peut avoir des liaisons de données sur plusieurs de ses propriétés. Toutefois, chaque vue ne peut avoir qu’une seule `BindingContext` , de sorte que plusieurs liaisons de données sur cette vue doivent toutes les propriétés de référence du même objet.

La solution à ce problème et à d’autres problèmes implique la `Mode` propriété, qui est définie sur un membre de l' `BindingMode` énumération :

- `Default`
- `OneWay`: les valeurs sont transférées de la source à la cible
- `OneWayToSource`: les valeurs sont transférées de la cible à la source.
- `TwoWay`: les valeurs sont transférées dans les deux sens entre la source et la cible
- `OneTime`— les données passent de la source à la cible, mais uniquement lorsque les `BindingContext` modifications

Le programme suivant illustre une utilisation courante des `OneWayToSource` modes de `TwoWay` liaison et. Quatre `Slider` vues sont conçues pour contrôler les `Scale` `Rotate` Propriétés,, `RotateX` et `RotateY` d’un `Label` . Au début, il semblerait que ces quatre propriétés du `Label` doivent être des cibles de liaison de données, car chacune est définie par un `Slider` . Toutefois, le `BindingContext` de ne `Label` peut être qu’un seul objet et il y a quatre curseurs différents.

Pour cette raison, toutes les liaisons sont définies de manière apparemment en arrière : le `BindingContext` de chacun des quatre curseurs a la valeur `Label` , et les liaisons sont définies sur les `Value` Propriétés des curseurs. En utilisant les `OneWayToSource` `TwoWay` modes et, ces `Value` propriétés peuvent définir les propriétés de la source, qui sont les `Scale` Propriétés,, `Rotate` `RotateX` et `RotateY` de l’entité `Label` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />

        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

Les liaisons sur trois des `Slider` vues sont `OneWayToSource` , ce qui signifie que la `Slider` valeur entraîne une modification de la propriété de son `BindingContext` , qui est le `Label` nommé `label` . Ces trois `Slider` vues entraînent des modifications apportées aux `Rotate` `RotateX` Propriétés, et `RotateY` de `Label` .

Toutefois, la liaison de la `Scale` propriété est `TwoWay` . Cela est dû au fait que la `Scale` propriété a une valeur par défaut de 1 et que l’utilisation d’une `TwoWay` liaison entraîne la définition de la `Slider` valeur initiale à 1 plutôt que 0. Si cette liaison était `OneWayToSource` , la `Scale` valeur par défaut de la propriété serait définie sur 0 `Slider` . Le `Label` n’est pas visible et peut entraîner une certaine confusion pour l’utilisateur.

 [![Liaisons vers l’arrière](data-binding-basics-images/slidertransforms.png)](data-binding-basics-images/slidertransforms-large.png#lightbox)

 > [!NOTE]
 > La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe possède également [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) les [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) Propriétés et, qui regroupent `VisualElement` respectivement les axes x et y.

## <a name="bindings-and-collections"></a>Liaisons et collections

Rien n’illustre la puissance du XAML et des liaisons de données mieux qu’un modèle `ListView` .

`ListView`définit une `ItemsSource` propriété de type `IEnumerable` et affiche les éléments de cette collection. Ces éléments peuvent être des objets de n’importe quel type. Par défaut, `ListView` utilise la `ToString` méthode de chaque élément pour afficher cet élément. Parfois, c’est exactement ce que vous voulez, mais dans de nombreux cas, `ToString` retourne uniquement le nom de classe complet de l’objet.

Toutefois, les éléments de la `ListView` collection peuvent être affichés comme vous le souhaitez par le biais de l’utilisation d’un *modèle*, qui implique une classe qui dérive de `Cell` . Le modèle est cloné pour chaque élément du `ListView` , et les liaisons de données qui ont été définies sur le modèle sont transférées vers les clones individuels.

Très souvent, vous souhaiterez créer une cellule personnalisée pour ces éléments à l’aide de la `ViewCell` classe. Ce processus est quelque peu confus dans le code, mais dans XAML, il devient très simple.

Le projet XamlSamples contient une classe appelée `NamedColor` . Chaque `NamedColor` objet possède `Name` `FriendlyName` des propriétés et de type `string` , et une `Color` propriété de type `Color` . En outre, `NamedColor` contient 141 champs en lecture seule statiques de type `Color` correspondant aux couleurs définies dans la Xamarin.Forms `Color` classe. Un constructeur statique crée une `IEnumerable<NamedColor>` collection qui contient `NamedColor` des objets correspondant à ces champs statiques et l’assigne à sa propriété statique publique `All` .

L’affectation de la valeur `NamedColor.All` à la propriété statique `ItemsSource` de `ListView` est simple à l’aide de l' `x:Static` extension de balisage :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

L’affichage résultant établit que les éléments sont véritablement de type `XamlSamples.NamedColor` :

[![Liaison à une collection](data-binding-basics-images/listview1.png)](data-binding-basics-images/listview1-large.png#lightbox)

Il ne s’agit pas d’une grande quantité d’informations, mais il est possible de `ListView` faire défiler et de sélectionner.

Pour définir un modèle pour les éléments, vous pouvez décomposer la `ItemTemplate` propriété en tant qu’élément de propriété et lui affecter une valeur `DataTemplate` , qui fait référence à un `ViewCell` . À la `View` propriété du `ViewCell` , vous pouvez définir une disposition d’un ou plusieurs affichages pour afficher chaque élément. Voici un exemple simple :

```xaml
<ListView ItemsSource="{x:Static local:NamedColor.All}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.View>
                    <Label Text="{Binding FriendlyName}" />
                </ViewCell.View>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

> [!NOTE]
> La source de liaison pour les cellules, et les enfants de cellules, est la `ListView.ItemsSource` collection.

L' `Label` élément a la valeur `View` de la propriété de `ViewCell` . (Les `ViewCell.View` balises ne sont pas nécessaires, car la `View` propriété est la propriété de contenu de `ViewCell` .) Ce balisage affiche la `FriendlyName` propriété de chaque `NamedColor` objet :

[![Liaison à une collection avec un DataTemplate](data-binding-basics-images/listview2.png)](data-binding-basics-images/listview2-large.png#lightbox)

Beaucoup mieux. À présent, il vous suffit d’embellir le modèle d’élément avec plus d’informations et la couleur réelle. Pour prendre en charge ce modèle, certaines valeurs et certains objets ont été définis dans le dictionnaire de ressources de la page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <OnPlatform x:Key="boxSize"
                        x:TypeArguments="x:Double">
                <On Platform="iOS, Android, UWP" Value="50" />
            </OnPlatform>

            <OnPlatform x:Key="rowHeight"
                        x:TypeArguments="x:Int32">
                <On Platform="iOS, Android, UWP" Value="60" />
            </OnPlatform>

            <local:DoubleToIntConverter x:Key="intConverter" />

        </ResourceDictionary>
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">

                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />

                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">

                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="Medium" />

                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Color.R,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat='R={0:X2}'}" />

                                <Label Text="{Binding Color.G,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', G={0:X2}'}" />

                                <Label Text="{Binding Color.B,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Notez l’utilisation de `OnPlatform` pour définir la taille d’un `BoxView` et la hauteur des `ListView` lignes. Bien que les valeurs de toutes les plateformes soient identiques, le balisage peut facilement être adapté à d’autres valeurs pour affiner l’affichage.

## <a name="binding-value-converters"></a>Convertisseurs de valeur de liaison

Le précédent fichier XAML de **démonstration ListView** affiche les `R` propriétés individuelles, `G` et `B` de la Xamarin.Forms `Color` structure. Ces propriétés sont de type `double` et sont comprises entre 0 et 1. Si vous souhaitez afficher les valeurs hexadécimales, vous ne pouvez pas simplement utiliser `StringFormat` avec une spécification de mise en forme « x2 ». Qui ne fonctionne que pour les entiers et en plus, les `double` valeurs doivent être multipliées par 255.

Ce petit problème a été résolu avec un *convertisseur de valeur*, également appelé convertisseur de *liaison*. Il s’agit d’une classe qui implémente l' `IValueConverter` interface, ce qui signifie qu’elle a deux méthodes nommées `Convert` et `ConvertBack` . La `Convert` méthode est appelée lorsqu’une valeur est transférée de la source à la cible ; la `ConvertBack` méthode est appelée pour les transferts de la cible vers la source dans les `OneWayToSource` `TwoWay` liaisons ou :

```csharp
using System;
using System.Globalization;
using Xamarin.Forms;

namespace XamlSamples
{
    class DoubleToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType,
                              object parameter, CultureInfo culture)
        {
            double multiplier;

            if (!Double.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (double)value);
        }

        public object ConvertBack(object value, Type targetType,
                                  object parameter, CultureInfo culture)
        {
            double divider;

            if (!Double.TryParse(parameter as string, out divider))
                divider = 1;

            return ((double)(int)value) / divider;
        }
    }
}
```

La `ConvertBack` méthode ne joue pas de rôle dans ce programme, car les liaisons ne sont qu’une seule façon de la source à la cible.

Une liaison fait référence à un convertisseur de liaison avec la `Converter` propriété. Un convertisseur de liaison peut également accepter un paramètre spécifié avec la `ConverterParameter` propriété. Pour une certaine polyvalence, il s’agit de la façon dont le multiplicateur est spécifié. Le convertisseur de liaison vérifie si le paramètre de convertisseur a une `double` valeur valide.

Le convertisseur est instancié dans le dictionnaire de ressources afin de pouvoir être partagé entre plusieurs liaisons :

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Trois liaisons de données référencent cette instance unique. Notez que l' `Binding` extension de balisage contient une `StaticResource` extension de balisage incorporée :

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Voici le résultat :

[![Liaison à une collection avec un DataTemplate et des convertisseurs](data-binding-basics-images/listview3.png)](data-binding-basics-images/listview3-large.png#lightbox)

Le `ListView` est assez sophistiqué pour gérer les modifications qui peuvent se produire dynamiquement dans les données sous-jacentes, mais uniquement si vous effectuez certaines étapes. Si la collection d’éléments assignée à la `ItemsSource` propriété des `ListView` modifications au moment de l’exécution (autrement dit, si des éléments peuvent être ajoutés ou supprimés de la collection), utilisez une `ObservableCollection` classe pour ces éléments. `ObservableCollection`implémente l' `INotifyCollectionChanged` interface et `ListView` installe un gestionnaire pour l' `CollectionChanged` événement.

Si les propriétés des éléments eux-mêmes sont modifiées pendant l’exécution, les éléments de la collection doivent implémenter l' `INotifyPropertyChanged` interface et les modifications de signal apportées aux valeurs de propriété à l’aide de l' `PropertyChanged` événement. Cela est illustré dans la partie suivante de cette série, [partie 5. De la liaison de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Résumé

Les liaisons de données fournissent un mécanisme puissant pour lier les propriétés entre deux objets dans une page, ou entre des objets visuels et des données sous-jacentes. Toutefois, lorsque l’application commence à travailler avec des sources de données, un modèle architectural d’application populaire commence à émerger comme un paradigme utile. Ce sujet est abordé dans la [partie 5. Des liaisons de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="related-links"></a>Liens connexes

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Prise en main avec XAML (exemple)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 2. Syntaxe XAML essentielle (exemple)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 3. Extensions de balisage XAML (exemple)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 5. De la liaison de données à MVVM (exemple)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
