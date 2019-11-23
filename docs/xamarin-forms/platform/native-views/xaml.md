---
title: Vues natives en XAML
description: Vues natives à partir d’iOS, Android et la plateforme Windows universelle peuvent être référencées directement à partir de fichiers de XAML de Xamarin.Forms. Propriétés et gestionnaires d’événements peuvent être définies sur les vues natives, et ils peuvent interagir avec les vues Xamarin.Forms. Cet article montre comment utiliser les vues natives à partir de fichiers de XAML de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/24/2016
ms.openlocfilehash: 3c4fa085c9fdf17cdc256d9710c23911bb60d584
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770645"
---
# <a name="native-views-in-xaml"></a>Vues natives en XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_Les vues natives d’iOS, Android et les plateforme Windows universelle peuvent être directement référencées à partir de fichiers XAML Xamarin. Forms. Les propriétés et les gestionnaires d’événements peuvent être définis sur des vues natives et interagir avec les vues Xamarin. Forms. Cet article montre comment utiliser des vues natives à partir de fichiers XAML Xamarin. Forms._

Cet article traite des sujets suivants :

- [Utilisation de vues natives](#consuming) – le processus pour l’utilisation d’une vue native à partir de XAML.
- [À l’aide de liaisons natifs](#native_bindings) – vers et à partir des propriétés de vues natives de liaison de données.
- [Passer des arguments aux vues natives](#passing_arguments) : passer des arguments aux constructeurs de mode natif et appelle des méthodes de fabrique de vue natif.
- [Référence aux vues natives à partir du code](#native_view_code) – récupération d’instances de la vue native déclaré dans un fichier XAML, à partir de son fichier code-behind.
- [Vues natives de sous-classement](#subclassing) – sous-classement de vues natives pour définir une API conviviale pour XAML.  

<a name="overview" />

## <a name="overview"></a>Overview

Pour incorporer une vue native dans un fichier XAML de Xamarin.Forms :

1. Ajouter un `xmlns` déclaration d’espace de noms dans le fichier XAML pour l’espace de noms qui contient la vue native.
1. Créez une instance de la vue native dans le fichier XAML.

> [!IMPORTANT]
> Le code XAML compilé doit être désactivé pour les pages XAML qui utilisent des vues natives. Pour ce faire, vous pouvez décorer la classe code-behind de votre page XAML avec l’attribut `[XamlCompilation(XamlCompilationOptions.Skip)]`. Pour plus d’informations sur la compilation XAML, consultez [compilation XAML dans Xamarin. Forms](~/xamarin-forms/xaml/xamlc.md).

Pour faire référence à une vue native à partir d’un fichier code-behind, vous devez utiliser un projet de ressource partagé (SAP) et encapsulez le code spécifique à la plateforme avec les directives de compilation conditionnelle. Pour plus d’informations, consultez [faisant référence aux vues natives à partir du Code](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Utilisation de vues natives

L’exemple de code suivant illustre la consommation des vues natives pour chaque plateforme pour un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static androidLocal:MainActivity.Instance}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

Ainsi que la `clr-namespace` et `assembly` pour un espace de noms du mode natif, un `targetPlatform` doit également être spécifié. Cela doit être défini sur une des valeurs de la [ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform) énumération et est généralement défini sur `iOS`, `Android`, ou `Windows`. Lors de l’exécution, l’analyseur XAML ignore les préfixes d’espace de noms XML qui ont un `targetPlatform` qui ne correspond pas à la plateforme sur laquelle l’application est en cours d’exécution.

Chaque déclaration d’espace de noms peut être utilisée pour référencer toute classe ou structure à partir de l’espace de noms spécifié. Par exemple, le `ios` déclaration d’espace de noms peut être utilisée pour référencer toute classe ou structure à partir d’iOS `UIKit` espace de noms. Propriétés du mode natif peuvent être définies via XAML, mais les types de propriété et objet doivent correspondre. Par exemple, le `UILabel.TextColor` propriété est définie sur `UIColor.Red` à l’aide de la `x:Static` extension de balisage et le `ios` espace de noms.

Propriétés pouvant être liées et des propriétés pouvant être liées attachées peuvent également être définies sur vues natives à l’aide de la `Class.BindableProperty="value"` syntaxe. Chaque vue natif est encapsulée dans un spécifique à la plateforme `NativeViewWrapper` instance, qui dérive de la [ `Xamarin.Forms.View` ](xref:Xamarin.Forms.View) classe. Définition d’une propriété pouvant être liée ou la propriété peut être liée jointe sur une vue native transfère la valeur de propriété au wrapper. Par exemple, une mise en page centrée horizontal peut être spécifié en définissant `View.HorizontalOptions="Center"` sur le mode natif.

> [!NOTE]
> Notez que les styles ne peut pas être utilisés avec des vues natives, étant donné que les styles peuvent cibler uniquement les propriétés qui sont soutenues par `BindableProperty` objets.

Constructeurs de widget Android nécessitent généralement la Android `Context` de l’objet comme un argument et cela peuvent être remis via une propriété statique dans le `MainActivity` classe. Par conséquent, lorsque vous créez un widget d’Android dans XAML, le `Context` objet doit généralement être passé à constructeur son à l’aide de la `x:Arguments` attribut avec un `x:Static` extension de balisage. Pour plus d’informations, consultez [en passant les Arguments de vues natives](#passing_arguments).

> [!NOTE]
> Notez cette attribution de noms une vue native avec `x:Name` n’est pas possible dans un projet de bibliothèque .NET Standard ou un projet de ressource partagé (SAP). Cela générera une variable du type natif, ce qui provoque une erreur de compilation. Toutefois, les vues natives peuvent être encapsulées dans `ContentView` instances et récupérées dans le fichier code-behind, si un SAP est utilisé. Pour plus d’informations, consultez [faisant référence à une vue natif à partir du Code](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Liaisons natifs

Liaison de données est utilisée pour synchroniser une interface utilisateur avec sa source de données et simplifie la façon dont une application Xamarin.Forms affiche et interagit avec ses données. Condition que l’objet source implémente le `INotifyPropertyChanged` interface, les modifications dans le *source* objet sont automatiquement envoyées à la *cible* objet par l’infrastructure de liaison et les modifications dans le *cible* objet peut éventuellement être envoyé à la *source* objet.

Propriétés de vues natives peuvent également utiliser la liaison de données. L’exemple de code suivant illustre la liaison de données à l’aide des propriétés de vues natives :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeSwitch"
        x:Class="NativeSwitch.NativeSwitchPage">
    <StackLayout Margin="20">
        <Label Text="Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <Entry Placeholder="This Entry is bound to the native switch" IsEnabled="{Binding IsSwitchOn}" />
        <ios:UISwitch On="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=ValueChanged}"
            OnTintColor="{x:Static ios:UIColor.Red}"
            ThumbTintColor="{x:Static ios:UIColor.Blue}" />
        <androidWidget:Switch x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

La page contient un [ `Entry` ](xref:Xamarin.Forms.Entry) dont [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriété est liée à la `NativeSwitchPageViewModel.IsSwitchOn` propriété. Le [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la page est définie sur une nouvelle instance de la `NativeSwitchPageViewModel` classe dans le fichier code-behind, avec la classe ViewModel implémentant le `INotifyPropertyChanged` interface.

La page contient également un commutateur natif pour chaque plateforme. Chaque commutateur natif utilise une [ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay) liaison à mettre à jour la valeur de la `NativeSwitchPageViewModel.IsSwitchOn` propriété. Par conséquent, lorsque le commutateur est désactivé, le `Entry` est désactivé, et lorsque le commutateur est activé, le `Entry` est activé. Les captures d’écran suivantes montrent cette fonctionnalité sur chaque plateforme :

![](xaml-images/native-switch-disabled.png "Commutateur natif désactivé")
![](xaml-images/native-switch-enabled.png "commutateur natif activé")

Liaisons bidirectionnelles sont automatiquement pris en charge si la propriété native implémente `INotifyPropertyChanged`, prend en charge de la clé-valeur en observant (KVO) sur iOS, ou est un `DependencyProperty` sur UWP. Toutefois, nombreuses vues natives ne prennent en charge la notification de modification de propriété. Pour ces vues, vous pouvez spécifier un [ `UpdateSourceEventName` ](xref:Xamarin.Forms.Binding.UpdateSourceEventName) valeur de propriété dans le cadre de l’expression de liaison. Cette propriété doit être définie sur le nom d’un événement dans la vue native qui signale lorsque la propriété cible a été modifiée. Ensuite, lorsque la valeur du commutateur natif est modifié, le `Binding` classe est avertie que l’utilisateur a modifié la valeur du commutateur et le `NativeSwitchPageViewModel.IsSwitchOn` valeur de propriété est mise à jour.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Passage des Arguments aux vues natives

Arguments de constructeur peuvent être passés à des vues natives à l’aide de la `x:Arguments` attribut avec un `x:Static` extension de balisage. En outre, les méthodes de fabrique de mode natif (`public static` méthodes qui retournent des objets ou des valeurs du même type que la classe ou structure qui définit les méthodes) peut être appelée en spécifiant la méthode nom en utilisant le `x:FactoryMethod` attribut et ses arguments à l’aide de la `x:Arguments` attribut.

L’exemple de code suivant illustre ces deux techniques :

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winMedia="clr-namespace:Windows.UI.Xaml.Media;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winText="clr-namespace:Windows.UI.Text;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winui="clr-namespace:Windows.UI;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows">
        ...
        <ios:UILabel Text="Simple Native Color Picker" View.HorizontalOptions="Center">
            <ios:UILabel.Font>
                <ios:UIFont x:FactoryMethod="FromName">
                    <x:Arguments>
                        <x:String>Papyrus</x:String>
                        <x:Single>24</x:Single>
                    </x:Arguments>
                </ios:UIFont>
            </ios:UILabel.Font>
        </ios:UILabel>
        <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                    Text="Simple Native Color Picker"
                    TextSize="24"
                    View.HorizontalOptions="Center">
            <androidWidget:TextView.Typeface>
                <androidGraphics:Typeface x:FactoryMethod="Create">
                    <x:Arguments>
                        <x:String>cursive</x:String>
                        <androidGraphics:TypefaceStyle>Normal</androidGraphics:TypefaceStyle>
                    </x:Arguments>
                </androidGraphics:Typeface>
            </androidWidget:TextView.Typeface>
        </androidWidget:TextView>
        <winControls:TextBlock Text="Simple Native Color Picker"
                    FontSize="20"
                    FontStyle="{x:Static winText:FontStyle.Italic}"
                    View.HorizontalOptions="Center">
            <winControls:TextBlock.FontFamily>
                <winMedia:FontFamily>
                    <x:Arguments>
                        <x:String>Georgia</x:String>
                    </x:Arguments>
                </winMedia:FontFamily>
            </winControls:TextBlock.FontFamily>
        </winControls:TextBlock>
        ...
</ContentPage>
```

Le [ `UIFont.FromName` ](xref:UIKit.UIFont.FromName*) méthode de fabrique est utilisé pour définir le [ `UILabel.Font` ](xref:UIKit.UILabel.Font) à une nouvelle propriété [ `UIFont` ](xref:UIKit.UIFont) sur iOS. Le `UIFont` nom et la taille sont spécifiées par les arguments de méthode qui sont des enfants de le `x:Arguments` attribut.

Le [ `Typeface.Create` ](xref:Android.Graphics.Typeface.Create*) méthode de fabrique est utilisé pour définir le [ `TextView.Typeface` ](xref:Android.Widget.TextView.Typeface) à une nouvelle propriété [ `Typeface` ](xref:Android.Graphics.Typeface) sur Android. Le `Typeface` nom de famille et de style sont spécifiées par les arguments de méthode qui sont des enfants de le `x:Arguments` attribut.

Le [ `FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) constructeur est utilisé pour définir le [ `TextBlock.FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) propriété vers un nouveau `FontFamily` sur la plateforme Windows universelle (UWP). Le `FontFamily` nom est spécifié par l’argument de méthode qui est un enfant de le `x:Arguments` attribut.

> [!NOTE]
> Arguments doivent correspondre les types requis par la méthode de constructeur ou de la fabrique.

Les captures d’écran suivantes montrent le résultat de la spécification des arguments de méthode et de constructeur de fabrique pour définir la police sur différentes vues natives :

![](xaml-images/passing-arguments.png "Définition des polices de vues natives")

Pour plus d’informations sur le passage d’arguments dans XAML, consultez [en passant les Arguments dans XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Référence aux vues natives à partir du Code

Bien qu’il n’est pas possible de nommer une vue native avec le `x:Name` attribut, il est possible de récupérer une instance de la vue native déclarée dans un fichier XAML à partir de son fichier code-behind dans un projet d’accès partagé, sous réserve que le mode natif est un enfant d’un [ `ContentView` ](xref:Xamarin.Forms.ContentView) qui spécifie un `x:Name` valeur d’attribut. Ensuite, à l’intérieur de directives de compilation conditionnelle dans le fichier code-behind, vous devez :

1. Récupérer le [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) propriété valeur et des convertir en données spécifiques à la plateforme `NativeViewWrapper` type.
1. Récupérer le `NativeViewWrapper.NativeElement` propriété et effectuer un cast vers le type de mode natif.

L’API native peut ensuite être appelée sur le mode natif pour effectuer les opérations souhaitées. Cette approche offre également l’avantage que plusieurs vues natives XAML pour différentes plateformes peuvent être des enfants du même [ `ContentView` ](xref:Xamarin.Forms.ContentView). L’exemple de code suivant illustre cette technique :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Text in a TextView" />
              <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>
```

Dans l’exemple ci-dessus, les vues natives pour chaque plateforme sont des enfants [ `ContentView` ](xref:Xamarin.Forms.ContentView) contrôles, avec la `x:Name` valeur d’attribut qui est utilisé pour récupérer le `ContentView` dans le code-behind :

```csharp
public partial class NativeViewInsideContentViewPage : ContentPage
{
    public NativeViewInsideContentViewPage()
    {
        InitializeComponent();

#if __IOS__
        var wrapper = (Xamarin.Forms.Platform.iOS.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (UIKit.UIButton)wrapper.NativeView;
        button.SetTitle("Scale and Rotate Text", UIKit.UIControlState.Normal);
        button.SetTitleColor(UIKit.UIColor.Black, UIKit.UIControlState.Normal);
#endif
#if __ANDROID__
        var wrapper = (Xamarin.Forms.Platform.Android.NativeViewWrapper)contentViewTextParent.Content;
        var textView = (Android.Widget.TextView)wrapper.NativeView;
        textView.SetTextColor(Android.Graphics.Color.Red);
#endif
#if WINDOWS_UWP
        var textWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewTextParent.Content;
        var textBlock = (Windows.UI.Xaml.Controls.TextBlock)textWrapper.NativeElement;
        textBlock.Foreground = new Windows.UI.Xaml.Media.SolidColorBrush(Windows.UI.Colors.Red);
        var buttonWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (Windows.UI.Xaml.Controls.Button)buttonWrapper.NativeElement;
        button.Click += (sender, args) => OnButtonTap(sender, EventArgs.Empty);
#endif
    }

    async void OnButtonTap(object sender, EventArgs e)
    {
        contentViewButtonParent.Content.IsEnabled = false;
        contentViewTextParent.Content.ScaleTo(2, 2000);
        await contentViewTextParent.Content.RotateTo(360, 2000);
        contentViewTextParent.Content.ScaleTo(1, 2000);
        await contentViewTextParent.Content.RelRotateTo(360, 2000);
        contentViewButtonParent.Content.IsEnabled = true;
    }
}
```

Le [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) propriété est accessible pour récupérer la vue native encapsulée comme spécifiques à la plateforme `NativeViewWrapper` instance. Le `NativeViewWrapper.NativeElement` propriété est ensuite accessible pour récupérer la vue native comme son type natif. Les API natives de la vue est ensuite appelée pour effectuer les opérations souhaitées.

IOS et Android boutons natif partagent le même `OnButtonTap` Gestionnaire d’événements, car chaque bouton natif consomme un `EventHandler` déléguer en réponse à un événement tactile. Toutefois, la plateforme universelle Windows (UWP) utilise un distinct `RoutedEventHandler`, qui à son tour consomme la `OnButtonTap` Gestionnaire d’événements dans cet exemple. Par conséquent, quand un bouton natif est activé, le `OnButtonTap` Gestionnaire d’événements s’exécute, ce qui met à l’échelle et fait pivoter le contrôle natif contenu dans le [ `ContentView` ](xref:Xamarin.Forms.ContentView) nommé `contentViewTextParent`. Les captures d’écran suivantes montrent ce qui se produisent sur chaque plateforme :

![](xaml-images/contentview.png "ContentView contenant un contrôle natif")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>Vues natives sous-classement

Nombreux iOS et Android vues natives ne sont pas adaptées à l’instanciation dans XAML, car ils utilisent des méthodes, plutôt que des propriétés, pour configurer le contrôle. La solution à ce problème consiste à vues natives de sous-classe dans les wrappers qui définissent une API plus de XAML conviviale qui utilise des propriétés pour le contrôle d’installation, et qui utilise les événements indépendant de la plateforme. Les vues natives encapsulés pouvant être placés dans un projet de ressource partagé (SAP) et entourés de directives de compilation conditionnelle, ou placés dans des projets spécifiques à la plateforme et référencés à partir de XAML dans un projet de bibliothèque .NET Standard.

L’exemple de code suivant montre une page de Xamarin.Forms consomme sous-classé vues natives :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SubclassedNativeControls.Droid;assembly=SubclassedNativeControls.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:SubclassedNativeControls"
        x:Class="SubclassedNativeControls.SubclassedNativeControlsPage">
    <StackLayout Margin="20">
        <Label Text="Subclassed Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <StackLayout Orientation="Horizontal">
          <Label Text="You have chosen:" />
          <Label Text="{Binding SelectedFruit}" />      
        </StackLayout>
        <iosLocal:MyUIPickerView ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectedItemChanged}" />
        <androidLocal:MySpinner x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

La page contient un [ `Label` ](xref:Xamarin.Forms.Label) qui affiche les fruits choisis par l’utilisateur à partir d’un contrôle natif. Le `Label` lie à la `SubclassedNativeControlsPageViewModel.SelectedFruit` propriété. Le [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la page est définie sur une nouvelle instance de la `SubclassedNativeControlsPageViewModel` classe dans le fichier code-behind, avec la classe ViewModel implémentant le `INotifyPropertyChanged` interface.

La page contient également une vue de sélecteur natif pour chaque plateforme. Chaque vue natif affiche la collection de fruits en liant ses `ItemSource` propriété le `SubclassedNativeControlsPageViewModel.Fruits` collection. Cela permet à l’utilisateur de choisir un fruit, comme indiqué dans les captures d’écran suivante :

![](xaml-images/sub-classed.png "Vues natives sous-classées")

Sur iOS et Android les sélecteurs natifs utilisent des méthodes pour configurer les contrôles. Par conséquent, ces sélecteurs doivent être sous-classée pour exposer des propriétés pour les rendre compatibles avec le XAML. Sur la plateforme de Windows universelle (UWP), le `ComboBox` est déjà compatible avec XAML et ne nécessitent donc pas sous-classement.

### <a name="ios"></a>iOS

Les sous-classes de la mise en œuvre iOS le [ `UIPickerView` ](xref:UIKit.UIPickerView) vue et expose des propriétés et un événement qui peut être facilement utilisé à partir de XAML :

```csharp
public class MyUIPickerView : UIPickerView
{
    public event EventHandler<EventArgs> SelectedItemChanged;

    public MyUIPickerView()
    {
        var model = new PickerModel();
        model.ItemChanged += (sender, e) =>
        {
            if (SelectedItemChanged != null)
            {
                SelectedItemChanged.Invoke(this, e);
            }
        };
        Model = model;
    }

    public IList<string> ItemsSource
    {
        get
        {
            var pickerModel = Model as PickerModel;
            return (pickerModel != null) ? pickerModel.Items : null;
        }
        set
        {
            var model = Model as PickerModel;
            if (model != null)
            {
                model.Items = value;
            }
        }
    }

    public string SelectedItem
    {
        get { return (Model as PickerModel).SelectedItem; }
        set { }
    }
}
```

Le `MyUIPickerView` classe expose `ItemsSource` et `SelectedItem` propriétés et un `SelectedItemChanged` événement. Un [ `UIPickerView` ](xref:UIKit.UIPickerView) nécessite un sous-jacent [ `UIPickerViewModel` ](xref:UIKit.UIPickerViewModel) modèle de données, qui est accessible par le `MyUIPickerView` propriétés et événements. Le `UIPickerViewModel` modèle de données est fourni par la `PickerModel` classe :

```csharp
class PickerModel : UIPickerViewModel
{
    int selectedIndex = 0;
    public event EventHandler<EventArgs> ItemChanged;
    public IList<string> Items { get; set; }

    public string SelectedItem
    {
        get
        {
            return Items != null && selectedIndex >= 0 && selectedIndex < Items.Count ? Items[selectedIndex] : null;
        }
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return Items != null ? Items.Count : 0;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        return Items != null && Items.Count > row ? Items[(int)row] : null;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 1;
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        selectedIndex = (int)row;
        if (ItemChanged != null)
        {
            ItemChanged.Invoke(this, new EventArgs());
        }
    }
}
```

Le `PickerModel` classe fournit le stockage sous-jacent pour le `MyUIPickerView` (classe), via le `Items` propriété. Chaque fois que l’élément sélectionné dans le `MyUIPickerView` modifications, la [ `Selected` ](xref:UIKit.UIPickerViewModel.Selected*) méthode est exécutée, les mises à jour l’index sélectionné et déclenche le `ItemChanged` événement. Cela garantit que le `SelectedItem` propriété retourne toujours le dernier élément sélectionné par l’utilisateur. En outre, le `PickerModel` classe substitue des méthodes qui sont utilisées pour le programme d’installation le `MyUIPickerView` instance.

### <a name="android"></a>Android

Les sous-classes de la mise en œuvre Android le [ `Spinner` ](xref:Android.Widget.Spinner) vue et expose des propriétés et un événement qui peut être facilement utilisé à partir de XAML :

```csharp
class MySpinner : Spinner
{
    ArrayAdapter adapter;
    IList<string> items;

    public IList<string> ItemsSource
    {
        get { return items; }
        set
        {
            if (items != value)
            {
                items = value;
                adapter.Clear();

                foreach (string str in items)
                {
                    adapter.Add(str);
                }
            }
        }
    }

    public string SelectedObject
    {
        get { return (string)GetItemAtPosition(SelectedItemPosition); }
        set
        {
            if (items != null)
            {
                int index = items.IndexOf(value);
                if (index != -1)
                {
                    SetSelection(index);
                }
            }
        }
    }

    public MySpinner(Context context) : base(context)
    {
        ItemSelected += OnBindableSpinnerItemSelected;

        adapter = new ArrayAdapter(context, Android.Resource.Layout.SimpleSpinnerItem);
        adapter.SetDropDownViewResource(Android.Resource.Layout.SimpleSpinnerDropDownItem);
        Adapter = adapter;
    }

    void OnBindableSpinnerItemSelected(object sender, ItemSelectedEventArgs args)
    {
        SelectedObject = (string)GetItemAtPosition(args.Position);
    }
}
```

Le `MySpinner` classe expose `ItemsSource` et `SelectedObject` propriétés et un `ItemSelected` événement. Les éléments affichés par le `MySpinner` classe sont fournies par le [ `Adapter` ](xref:Android.Widget.Adapter) associé à la vue et les éléments sont renseignées dans la `Adapter` lorsque le `ItemsSource` propriété est d’abord définie. Chaque fois que l’élément sélectionné dans le `MySpinner` classe les modifications, la `OnBindableSpinnerItemSelected` Gestionnaire d’événements met à jour le `SelectedObject` propriété.

## <a name="summary"></a>Résumé

Cet article a montré comment utiliser des vues natives à partir de fichiers de XAML de Xamarin.Forms. Propriétés et gestionnaires d’événements peuvent être définies sur les vues natives, et ils peuvent interagir avec les vues Xamarin.Forms.

## <a name="related-links"></a>Liens connexes

- [NativeSwitch (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Formulaires natifs](~/xamarin-forms/platform/native-forms.md)
- [Passage des Arguments dans XAML](~/xamarin-forms/xaml/passing-arguments.md)
