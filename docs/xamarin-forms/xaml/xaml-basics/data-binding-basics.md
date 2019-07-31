---
title: Partie 4. Principes fondamentaux de liaison de données
description: Liaisons de données permettent les propriétés de deux objets à lier afin qu’une modification dans une provoque une modification dans l’autre.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: f5c5f0449962dec45a521112b2de92cddefe453f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655255"
---
# <a name="part-4-data-binding-basics"></a>Partie 4. Principes fondamentaux de liaison de données

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Liaisons de données permettent les propriétés de deux objets à lier afin qu’une modification dans une provoque une modification dans l’autre. Il s’agit d’un outil très précieux, et tandis que les liaisons de données peuvent être définis entièrement en code, XAML fournit des raccourcis et convivialité. Par conséquent, les extensions de balisage plus importantes dans Xamarin.Forms sont lié._

## <a name="data-bindings"></a>Liaisons de données

Liaisons de données connectent des propriétés de deux objets, appelés le *source* et *cible*. Dans le code, deux étapes sont requises: La propriété de l’objet cible doit être définie sur l’objet source et la `SetBinding` méthode (souvent utilisée conjointement avec la `Binding` classe) doit être appelée sur l’objet cible pour lier une propriété de cet objet à une propriété de la source. `BindingContext` dessin.

La propriété cible doit être une propriété pouvant être liée, ce qui signifie que l’objet cible doit dériver de `BindableObject`. La documentation en ligne de Xamarin.Forms indique quelles propriétés sont des propriétés pouvant être liées. Une propriété de `Label` comme `Text` est associé à la propriété peut être liée `TextProperty`.

Dans le balisage, vous devez également effectuer les mêmes étapes qui sont requises dans le code, à ceci près que le `Binding` remplace l’extension de balisage de la `SetBinding` appeler et `Binding` classe.

Toutefois, lorsque vous définissez des liaisons de données dans XAML, il existe plusieurs méthodes pour définir le `BindingContext` de l’objet cible. Parfois, il est défini à partir du fichier code-behind, parfois l’utilisation d’un `StaticResource` ou `x:Static` extension de balisage et parfois en tant que le contenu de `BindingContext` balises d’élément de propriété.

Liaisons sont plus souvent utilisées pour se connecter les éléments visuels d’un programme avec un modèle de données sous-jacent, généralement dans une réalisation de l’architecture des applications MVVM (Model-View-ViewModel), comme indiqué dans [partie 5. Liaisons de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), mais d’autres scénarios sont possibles.

## <a name="view-to-view-bindings"></a>Pour consulter les liaisons

Vous pouvez définir des liaisons de données pour lier les propriétés des deux vues dans la même page. Dans ce cas, vous définissez le `BindingContext` de l’objet cible en utilisant le `x:Reference` extension de balisage.

Ici est un fichier XAML qui contient un `Slider` et deux `Label` vues, un d'entre eux est pivoté par le `Slider` valeur et une autre qui affiche le `Slider` valeur :

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

Le `Slider` contient un `x:Name` attribut qui est référencé par les deux `Label` vues à l’aide de la `x:Reference` extension de balisage.

Le `x:Reference` extension de liaison définit une propriété nommée `Name` pour définir le nom de l’élément référencé, dans ce cas `slider`. Toutefois, le `ReferenceExtension` classe qui définit le `x:Reference` extension de balisage définit également un `ContentProperty` attribut `Name`, ce qui signifie qu’il n’est pas explicitement requis. Juste pour diverses, la première `x:Reference` inclut « nom = « mais pas la seconde :

```xaml
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

Le `Binding` extension de balisage elle-même peut avoir plusieurs propriétés, comme le `BindingBase` et `Binding` classe. Le `ContentProperty` pour `Binding` est `Path`, mais le « chemin d’accès = « partie de l’extension de balisage peut être omis si le chemin d’accès est le premier élément dans le `Binding` extension de balisage. Le premier exemple a « chemin d’accès = « mais le deuxième exemple omet :

```xaml
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Les propriétés peuvent tous être sur une seule ligne ou divisée en plusieurs lignes :

```xaml
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Faire tout ce qui est pratique.

Notez que le `StringFormat` propriété dans la seconde `Binding` extension de balisage. Dans Xamarin.Forms, les liaisons n’effectuent pas de conversion de type implicite, et si vous avez besoin afficher un objet autre qu’une chaîne sous forme de chaîne vous devez fournir un convertisseur de type ou utiliser `StringFormat`. Dans les coulisses, la méthode statique `String.Format` méthode est utilisée pour implémenter `StringFormat`. Qui est potentiellement un problème, car les spécifications de mise en forme .NET impliquent des accolades, qui sont également utilisés pour délimiter des extensions de balisage. Cette opération crée un risque de confusion de l’analyseur XAML. Pour éviter cela, placez la chaîne de mise en forme entière entre guillemets simples :

```xaml
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Voici le programme en cours d’exécution :

[![](data-binding-basics-images/sliderbinding.png "Pour consulter les liaisons")](data-binding-basics-images/sliderbinding-large.png#lightbox "pour consulter les liaisons ")

## <a name="the-binding-mode"></a>Le Mode de liaison

Une vue unique peut avoir des liaisons de données sur plusieurs de ses propriétés. Toutefois, chaque vue peut avoir un seul `BindingContext`, de sorte que plusieurs liaisons de données sur cette vue doivent toutes référencer des propriétés du même objet.

La solution à ces informations et autres problèmes implique le `Mode` propriété, qui est définie à un membre de la `BindingMode` énumération :

- `Default`
- `OneWay` — les valeurs sont transférés de la source à la cible
- `OneWayToSource` — les valeurs sont transférés de la cible à la source
- `TwoWay` — les valeurs sont transférés les deux sens entre source et cible
- `OneTime`— les données passent de la source à la cible, mais `BindingContext` uniquement lorsque les modifications

Le programme suivant montre une utilisation courante de la `OneWayToSource` et `TwoWay` modes de liaison. Quatre `Slider` vues sont destinées au contrôle de la `Scale`, `Rotate`, `RotateX`, et `RotateY` propriétés d’un `Label`. Dans un premier temps, il semble que si ces quatre propriétés de la `Label` doit être des cibles de liaison de données, car chacun est définie un `Slider`. Toutefois, le `BindingContext` de `Label` peut être qu’un seul objet, et il existe quatre curseurs différents.

Pour cette raison, toutes les liaisons sont définies de manière apparemment en arrière: Le `BindingContext` de chacun des quatre curseurs a la `Label`valeur, et les liaisons sont définies sur les `Value` propriétés des curseurs. À l’aide de la `OneWayToSource` et `TwoWay` modes, ces `Value` propriétés peuvent définir les propriétés de la source, qui sont le `Scale`, `Rotate`, `RotateX`, et `RotateY` propriétés de la `Label`:

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

Les liaisons sur trois de la `Slider` vues sont `OneWayToSource`, ce qui signifie qui les `Slider` valeur provoque une modification dans la propriété de son `BindingContext`, qui est la `Label` nommé `label`. Ces trois `Slider` vues entraînent des modifications à la `Rotate`, `RotateX`, et `RotateY` propriétés de la `Label`.

Toutefois, la liaison pour le `Scale` propriété est `TwoWay`. Il s’agit, car le `Scale` propriété a la valeur par défaut 1 et à l’aide un `TwoWay` liaison provoque la `Slider` initiale valeur à définir à 1 au lieu de 0. Si cette liaison ont été `OneWayToSource`, le `Scale` propriété est initialement définie sur 0 à partir de la `Slider` valeur par défaut. Le `Label` ne serait pas visible, et qui peut entraîner une certaine confusion pour l’utilisateur.

 [![](data-binding-basics-images/slidertransforms.png "Vers l’arrière liaisons")](data-binding-basics-images/slidertransforms-large.png#lightbox "descendante liaisons")

 > [!NOTE]
 > Le [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe a également [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) et [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) propriétés, laquelle mettre à l’échelle le `VisualElement` sur les axes x et y respectivement.

## <a name="bindings-and-collections"></a>Liaisons et des Collections

Rien n’illustre la puissance des liaisons XAML et données mieux qu’un basé sur un modèle `ListView`.

`ListView` définit un `ItemsSource` propriété de type `IEnumerable`, et il affiche les éléments dans cette collection. Ces éléments peuvent être des objets de tout type. Par défaut, `ListView` utilise le `ToString` (méthode) de chaque élément pour afficher cet élément. Parfois, il s’agit simplement ce que vous voulez, mais dans de nombreux cas, `ToString` retourne uniquement le nom de classe qualifié complet de l’objet.

Toutefois, les éléments dans le `ListView` collection peut être affichée comme vous le souhaitez à l’aide d’un *modèle*, qui implique une classe qui dérive de `Cell`. Le modèle est dupliqué pour chaque élément dans le `ListView`, et les liaisons de données qui ont été définis sur le modèle sont transférés vers les clones individuels.

Très souvent, vous souhaiterez créer une cellule personnalisée pour ces éléments à l’aide de la `ViewCell` classe. Ce processus est quelque peu compliquée dans le code, mais il devient très simple dans XAML.

Le XamlSamples projet comporte une classe appelée `NamedColor`. Chaque `NamedColor` objet a `Name` et `FriendlyName` propriétés de type `string`et un `Color` propriété de type `Color`. En outre, `NamedColor` a 141 en lecture seule les champs statiques de type `Color` correspondant aux couleurs définies dans le Xamarin.Forms `Color` classe. Un constructeur statique crée une `IEnumerable<NamedColor>` collection qui contient `NamedColor` objets correspondant à ces champs statiques et l’assigne à son statique public `All` propriété.

Définition de la méthode statique `NamedColor.All` propriété le `ItemsSource` d’un `ListView` est facile à l’aide de la `x:Static` extension de balisage :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

L’affichage résultant établit que les éléments sont véritablement de type `XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "Liaison à une Collection")](data-binding-basics-images/listview1-large.png#lightbox "liaison à une Collection")

Il n’est pas beaucoup plus d’informations, mais le `ListView` est permettant le défilement et sélectionnable.

Pour définir un modèle pour les éléments, vous souhaitez répartir le `ItemTemplate` propriété comme un élément de propriété et affectez-lui un `DataTemplate`, qui référence puis un `ViewCell`. Pour le `View` propriété de la `ViewCell` vous pouvez définir une disposition d’une ou plusieurs vues pour afficher chaque élément. Voici un exemple simple :

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
> La source de liaison pour les cellules, et les enfants de cellules `ListView.ItemsSource` , est la collection.

Le `Label` élément est défini sur le `View` propriété de la `ViewCell`. (Le `ViewCell.View` balises ne sont pas nécessaires, car le `View` propriété est la propriété de contenu de `ViewCell`.) Ce balisage affiche le `FriendlyName` propriété de chaque `NamedColor` objet :

[![](data-binding-basics-images/listview2.png "Liaison à une Collection avec un DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "liaison à une Collection avec un DataTemplate")

Beaucoup mieux. Tout ce qui est nécessaire est maintenant embellir le modèle d’élément avec plus d’informations et la couleur réelle. Pour prendre en charge ce modèle, certaines valeurs et les objets ont été définis dans le dictionnaire de ressources de la page :

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

Remarquez l’utilisation de `OnPlatform` pour définir la taille d’un `BoxView` et la hauteur de la `ListView` lignes. Bien que les valeurs pour toutes les plateformes sont identiques, le balisage peut être facilement adapté pour les autres valeurs d’affiner l’affichage.

## <a name="binding-value-converters"></a>Convertisseurs de valeur de liaison

Le précédent **ListView démonstration** les fichiers XAML affiche l’individu `R`, `G`, et `B` propriétés de la Xamarin.Forms `Color` structure. Ces propriétés sont de type `double` et comprise entre 0 et 1. Si vous souhaitez afficher les valeurs hexadécimales, vous ne pouvez pas simplement utiliser `StringFormat` avec une spécification de mise en forme « X2 ». Cela fonctionne uniquement pour les entiers et en outre, le `double` valeurs doivent être multipliés par 255.

Ce petit problème a été résolu avec une *convertisseur de valeurs*, également appelé un *convertisseur de liaisons*. Il s’agit d’une classe qui implémente le `IValueConverter` interface, ce qui signifie qu’il existe deux méthodes nommées `Convert` et `ConvertBack`. Le `Convert` méthode est appelée lorsqu’une valeur est transférée à partir de la source vers la cible ; la `ConvertBack` méthode est appelée pour les transferts à partir de la cible à la source dans `OneWayToSource` ou `TwoWay` liaisons :

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

Le `ConvertBack` méthode ne joue aucun rôle dans ce programme, car les liaisons sont uniquement unidirectionnels à partir de la source vers cible.

Une liaison fait référence à un convertisseur de liaison avec le `Converter` propriété. Un convertisseur de liaison peut également accepter un paramètre spécifié avec le `ConverterParameter` propriété. Pour certains polyvalence, voici comment le multiplicateur est spécifié. Le convertisseur de liaison vérifie le paramètre de convertisseur pour valide `double` valeur.

Le convertisseur est instancié dans le dictionnaire de ressources afin qu’il peut être partagé entre plusieurs liaisons :

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Liaisons de données de trois faire référence à cette instance unique. Notez que le `Binding` extension de balisage contient incorporé `StaticResource` extension de balisage :

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Voici le résultat :

[![](data-binding-basics-images/listview3.png "Liaison à une Collection avec un DataTemplate et convertisseurs")](data-binding-basics-images/listview3-large.png#lightbox "liaison à une Collection avec un DataTemplate et des convertisseurs")

Le `ListView` est assez sophistiqué dans le traitement des modifications pouvant intervenir dynamiquement dans les données sous-jacentes, mais uniquement si vous effectuer certaines étapes. Si la collection d’éléments est affecté à la `ItemsSource` propriété du `ListView` modifications pendant l’exécution, qui est, si les éléments peuvent être ajoutés à ou supprimés de la collection : utiliser un `ObservableCollection` classe pour ces éléments. `ObservableCollection` implémente le `INotifyCollectionChanged` interface, et `ListView` installe un gestionnaire pour le `CollectionChanged` événement.

Si les propriétés des éléments eux-mêmes changent pendant l’exécution, les éléments dans la collection doivent implémenter le `INotifyPropertyChanged` interface et signaux des modifications aux valeurs de propriété à l’aide de la `PropertyChanged` événement. Cela est illustré dans la partie suivante de cette série, [partie 5. À partir de la liaison de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Récapitulatif

Liaisons de données fournissent un mécanisme puissant pour la liaison des propriétés entre deux objets dans une page, ou entre des objets visuels et des données sous-jacentes. Mais lorsque l’application commence à fonctionner avec des sources de données, un modèle d’application populaires architecture commence à émerger comme un paradigme utile. Cela est couvert dans [partie 5. Liaisons de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).



## <a name="related-links"></a>Liens associés

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Bien démarrer avec XAML (exemple)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 2. Syntaxe XAML essentielle (exemple)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 3. Extensions de balisage XAML (exemple)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 5. À partir de la liaison de données à MVVM (exemple)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
