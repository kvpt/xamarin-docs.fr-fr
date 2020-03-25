---
title: Vues natives en XAML
description: Vues natives à partir d’iOS, Android et la plateforme Windows universelle peuvent être référencées directement à partir de fichiers de XAML de Xamarin.Forms. Propriétés et gestionnaires d’événements peuvent être définies sur les vues natives, et ils peuvent interagir avec les vues Xamarin.Forms. Cet article montre comment utiliser les vues natives à partir de fichiers de XAML de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2019
ms.openlocfilehash: 6d3954f44cd769ed02535eb260b9952e81e67c98
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247624"
---
# <a name="native-views-in-xaml"></a>Vues natives en XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_Les vues natives d’iOS, Android et les plateforme Windows universelle peuvent être directement référencées à partir de fichiers XAML Xamarin. Forms. Les propriétés et les gestionnaires d’événements peuvent être définis sur des vues natives et interagir avec les vues Xamarin. Forms. Cet article montre comment utiliser des vues natives à partir de fichiers XAML Xamarin. Forms._

Cet article aborde les thèmes suivants :

- [Utilisation des vues natives](#consuming) – processus de consommation d’une vue native à partir de XAML.
- [À l’aide de liaisons natives](#native_bindings) : liaison de données vers et à partir des propriétés de vues natives.
- [Passage d’arguments à des vues natives](#passing_arguments) : passage des arguments aux constructeurs de vue natifs et appel des méthodes de fabrique d’affichage natives.
- [Référence à des affichages natifs à partir du code](#native_view_code) – récupération d’instances de vue Native déclarées dans un fichier XAML, à partir de son fichier code-behind.
- [Sous-classement des vues natives](#subclassing) : sous-classement des vues natives pour définir une API compatible XAML.  

<a name="overview" />

## <a name="overview"></a>Vue d’ensemble

Pour incorporer une vue native dans un fichier XAML de Xamarin.Forms :

1. Ajoutez une déclaration d’espace de noms `xmlns` dans le fichier XAML pour l’espace de noms qui contient la vue native.
1. Créez une instance de la vue native dans le fichier XAML.

> [!IMPORTANT]
> Le code XAML compilé doit être désactivé pour les pages XAML qui utilisent des vues natives. Pour ce faire, vous pouvez décorer la classe code-behind de votre page XAML avec l’attribut `[XamlCompilation(XamlCompilationOptions.Skip)]`. Pour plus d’informations sur la compilation XAML, consultez [compilation XAML dans Xamarin. Forms](~/xamarin-forms/xaml/xamlc.md).

Pour faire référence à une vue native à partir d’un fichier code-behind, vous devez utiliser un projet de ressource partagé (SAP) et encapsulez le code spécifique à la plateforme avec les directives de compilation conditionnelle. Pour plus d’informations, consultez [référencement des vues natives à partir du code](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Utilisation de vues natives

L’exemple de code suivant illustre l’utilisation de vues natives pour chaque plateforme dans un [`ContentPage`](xref:Xamarin.Forms.ContentPage)Xamarin. Forms :

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

En plus de spécifier les `clr-namespace` et `assembly` pour un espace de noms d’affichage natif, un `targetPlatform` doit également être spécifié. Elle doit être définie sur `iOS`, `Android`, `UWP`, `Windows` (ce qui équivaut à `UWP`), `macOS`, `GTK`, `Tizen`ou `WPF`. Lors de l’exécution, l’analyseur XAML ignore tous les préfixes d’espaces de noms XML qui ont un `targetPlatform` qui ne correspond pas à la plateforme sur laquelle l’application s’exécute.

Chaque déclaration d’espace de noms peut être utilisée pour référencer toute classe ou structure à partir de l’espace de noms spécifié. Par exemple, la déclaration d’espace de noms `ios` peut être utilisée pour référencer une classe ou une structure à partir de l’espace de noms iOS `UIKit`. Propriétés du mode natif peuvent être définies via XAML, mais les types de propriété et objet doivent correspondre. Par exemple, la propriété `UILabel.TextColor` est définie sur `UIColor.Red` à l’aide de l’extension de balisage `x:Static` et de l’espace de noms `ios`.

Les propriétés pouvant être liées et les propriétés pouvant être liées peuvent également être définies sur des vues natives à l’aide de la syntaxe `Class.BindableProperty="value"`. Chaque vue native est encapsulée dans une instance de `NativeViewWrapper` spécifique à la plateforme, qui dérive de la classe [`Xamarin.Forms.View`](xref:Xamarin.Forms.View) . Définition d’une propriété pouvant être liée ou la propriété peut être liée jointe sur une vue native transfère la valeur de propriété au wrapper. Par exemple, une disposition horizontale centrée peut être spécifiée en définissant `View.HorizontalOptions="Center"` sur la vue native.

> [!NOTE]
> Notez que les styles ne peuvent pas être utilisés avec les vues natives, car les styles peuvent uniquement cibler des propriétés qui sont soutenus par des objets `BindableProperty`.

Les constructeurs Android widget requièrent généralement l’objet `Context` Android comme argument, ce qui peut être rendu disponible par le biais d’une propriété statique dans la classe `MainActivity`. Par conséquent, lors de la création d’un widget Android en XAML, l’objet `Context` doit généralement être passé au constructeur du widget à l’aide de l’attribut `x:Arguments` avec une extension de balisage `x:Static`. Pour plus d’informations, consultez [passage d’arguments à des vues natives](#passing_arguments).

> [!NOTE]
> Notez que l’attribution d’un nom à une vue native avec `x:Name` n’est pas possible dans un projet de bibliothèque .NET Standard ou un projet de ressources partagées (SAP). Cela générera une variable du type natif, ce qui provoque une erreur de compilation. Toutefois, les vues natives peuvent être encapsulées dans des instances de `ContentView` et récupérées dans le fichier code-behind, à condition qu’un SAP soit utilisé. Pour plus d’informations, consultez [référence à une vue native à partir du code](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Liaisons natifs

Liaison de données est utilisée pour synchroniser une interface utilisateur avec sa source de données et simplifie la façon dont une application Xamarin.Forms affiche et interagit avec ses données. À condition que l’objet source implémente l’interface `INotifyPropertyChanged`, les modifications apportées à l’objet *source* sont automatiquement envoyées à l’objet *cible* par l’infrastructure de liaison, et les modifications apportées à l’objet *cible* peuvent éventuellement faire l’objet d’un push vers l’objet *source* .

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

La page contient un [`Entry`](xref:Xamarin.Forms.Entry) dont la propriété [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) est liée à la propriété `NativeSwitchPageViewModel.IsSwitchOn`. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page est définie sur une nouvelle instance de la classe `NativeSwitchPageViewModel` dans le fichier code-behind, avec la classe ViewModel qui implémente l’interface `INotifyPropertyChanged`.

La page contient également un commutateur natif pour chaque plateforme. Chaque commutateur natif utilise une liaison de [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) pour mettre à jour la valeur de la propriété `NativeSwitchPageViewModel.IsSwitchOn`. Par conséquent, lorsque le commutateur est désactivé, le `Entry` est désactivé et, lorsque le commutateur est activé, le `Entry` est activé. Les captures d’écran suivantes montrent cette fonctionnalité sur chaque plateforme :

![](xaml-images/native-switch-disabled.png "Native Switch Disabled")
![](xaml-images/native-switch-enabled.png "Native Switch Enabled")

Les liaisons bidirectionnelles sont prises en charge automatiquement, à condition que la propriété native implémente `INotifyPropertyChanged`ou prenne en charge l’observation de la valeur de clé (KVO) sur iOS, ou qu’il s’agit d’un `DependencyProperty` sur UWP. Toutefois, nombreuses vues natives ne prennent en charge la notification de modification de propriété. Pour ces vues, vous pouvez spécifier une valeur de propriété [`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName) dans le cadre de l’expression de liaison. Cette propriété doit être définie sur le nom d’un événement dans la vue native qui signale lorsque la propriété cible a été modifiée. Ensuite, lorsque la valeur du commutateur natif change, la classe `Binding` est informée que l’utilisateur a modifié la valeur du commutateur, et la valeur de la propriété `NativeSwitchPageViewModel.IsSwitchOn` est mise à jour.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Passage des Arguments aux vues natives

Les arguments de constructeur peuvent être passés à des vues natives à l’aide de l’attribut `x:Arguments` avec une extension de balisage `x:Static`. En outre, les méthodes de fabrique d’affichage natives (`public static` des méthodes qui retournent des objets ou des valeurs du même type que la classe ou la structure qui définit les méthodes) peuvent être appelées en spécifiant le nom de la méthode à l’aide de l’attribut `x:FactoryMethod` et de ses arguments à l’aide de l’attribut `x:Arguments`.

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

La méthode de fabrique [`UIFont.FromName`](xref:UIKit.UIFont.FromName*) permet de définir la propriété [`UILabel.Font`](xref:UIKit.UILabel.Font) sur un nouveau [`UIFont`](xref:UIKit.UIFont) sur iOS. Le nom et la taille de `UIFont` sont spécifiés par les arguments de méthode qui sont des enfants de l’attribut `x:Arguments`.

La méthode de fabrique [`Typeface.Create`](xref:Android.Graphics.Typeface.Create*) permet de définir la propriété [`TextView.Typeface`](xref:Android.Widget.TextView.Typeface) sur un nouveau [`Typeface`](xref:Android.Graphics.Typeface) sur Android. Le nom et le style de famille de `Typeface` sont spécifiés par les arguments de méthode qui sont des enfants de l’attribut `x:Arguments`.

Le constructeur [`FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) est utilisé pour affecter à la propriété [`TextBlock.FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) une nouvelle `FontFamily` sur le plateforme Windows universelle (UWP). Le nom de `FontFamily` est spécifié par l’argument de méthode qui est un enfant de l’attribut `x:Arguments`.

> [!NOTE]
> Arguments doivent correspondre les types requis par la méthode de constructeur ou de la fabrique.

Les captures d’écran suivantes montrent le résultat de la spécification des arguments de méthode et de constructeur de fabrique pour définir la police sur différentes vues natives :

![](xaml-images/passing-arguments.png "Setting Fonts on Native Views")

Pour plus d’informations sur le passage d’arguments en XAML, consultez [passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Référence aux vues natives à partir du Code

Bien qu’il ne soit pas possible de nommer une vue native avec l’attribut `x:Name`, il est possible de récupérer une instance d’affichage Native déclarée dans un fichier XAML à partir de son fichier code-behind dans un projet Access partagé, à condition que la vue Native soit un enfant d’un [`ContentView`](xref:Xamarin.Forms.ContentView) qui spécifie une valeur d’attribut `x:Name`. Ensuite, à l’intérieur de directives de compilation conditionnelle dans le fichier code-behind, vous devez :

1. Récupérez la valeur de la propriété [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) et effectuez un cast de celle-ci en un type de `NativeViewWrapper` spécifique à la plateforme.
1. Récupérez la propriété `NativeViewWrapper.NativeElement` et effectuez un cast de celle-ci en type d’affichage natif.

L’API native peut ensuite être appelée sur le mode natif pour effectuer les opérations souhaitées. Cette approche offre également l’avantage que plusieurs vues natives XAML pour différentes plateformes peuvent être des enfants du même [`ContentView`](xref:Xamarin.Forms.ContentView). L’exemple de code suivant illustre cette technique :

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

Dans l’exemple ci-dessus, les vues natives pour chaque plateforme sont les enfants des contrôles [`ContentView`](xref:Xamarin.Forms.ContentView) , avec la valeur d’attribut `x:Name` utilisée pour récupérer le `ContentView` dans le code-behind :

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

La propriété [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) est accessible pour récupérer la vue Native encapsulée en tant qu’instance `NativeViewWrapper` spécifique à la plateforme. La propriété `NativeViewWrapper.NativeElement` est ensuite accessible pour récupérer la vue Native comme son type natif. Les API natives de la vue est ensuite appelée pour effectuer les opérations souhaitées.

Les boutons iOS et Android natifs partagent le même `OnButtonTap` gestionnaire d’événements, car chaque bouton natif consomme un délégué `EventHandler` en réponse à un événement tactile. Toutefois, le plateforme Windows universelle (UWP) utilise un `RoutedEventHandler`distinct qui, à son tour, utilise le gestionnaire d’événements `OnButtonTap` dans cet exemple. Par conséquent, lorsque l’utilisateur clique sur un bouton natif, le gestionnaire d’événements `OnButtonTap` s’exécute, qui met à l’échelle et fait pivoter le contrôle natif contenu dans le [`ContentView`](xref:Xamarin.Forms.ContentView) nommé `contentViewTextParent`. Les captures d’écran suivantes montrent ce qui se produisent sur chaque plateforme :

![](xaml-images/contentview.png "ContentView Containing a Native Control")

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

La page contient un [`Label`](xref:Xamarin.Forms.Label) qui affiche les fruits choisis par l’utilisateur à partir d’un contrôle natif. Le `Label` est lié à la propriété `SubclassedNativeControlsPageViewModel.SelectedFruit`. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page est définie sur une nouvelle instance de la classe `SubclassedNativeControlsPageViewModel` dans le fichier code-behind, avec la classe ViewModel qui implémente l’interface `INotifyPropertyChanged`.

La page contient également une vue de sélecteur natif pour chaque plateforme. Chaque vue Native affiche la collection de fruits en liant sa propriété `ItemSource` à la collection `SubclassedNativeControlsPageViewModel.Fruits`. Cela permet à l’utilisateur de choisir un fruit, comme indiqué dans les captures d’écran suivante :

![](xaml-images/sub-classed.png "Subclassed Native Views")

Sur iOS et Android les sélecteurs natifs utilisent des méthodes pour configurer les contrôles. Par conséquent, ces sélecteurs doivent être sous-classée pour exposer des propriétés pour les rendre compatibles avec le XAML. Sur la plateforme Windows universelle (UWP), le `ComboBox` est déjà compatible avec le langage XAML et ne nécessite donc pas de sous-classement.

### <a name="ios"></a>iOS

L’implémentation iOS sous-classe la vue [`UIPickerView`](xref:UIKit.UIPickerView) et expose les propriétés et un événement qui peut être facilement consommé à partir de XAML :

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

La classe `MyUIPickerView` expose les propriétés `ItemsSource` et `SelectedItem`, ainsi qu’un événement `SelectedItemChanged`. Un [`UIPickerView`](xref:UIKit.UIPickerView) requiert un modèle de données [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) sous-jacent, accessible par les propriétés et l’événement de `MyUIPickerView`. Le modèle de données `UIPickerViewModel` est fourni par la classe `PickerModel` :

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

La classe `PickerModel` fournit le stockage sous-jacent pour la classe `MyUIPickerView`, via la propriété `Items`. Chaque fois que l’élément sélectionné dans l' `MyUIPickerView` change, la méthode [`Selected`](xref:UIKit.UIPickerViewModel.Selected*) est exécutée, ce qui met à jour l’index sélectionné et déclenche l’événement `ItemChanged`. Cela permet de s’assurer que la propriété `SelectedItem` retourne toujours le dernier élément choisi par l’utilisateur. En outre, la classe `PickerModel` substitue les méthodes utilisées pour configurer l’instance `MyUIPickerView`.

### <a name="android"></a>Android

L’implémentation Android sous-classe la vue [`Spinner`](xref:Android.Widget.Spinner) et expose les propriétés et un événement qui peut être facilement consommé à partir de XAML :

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

La classe `MySpinner` expose les propriétés `ItemsSource` et `SelectedObject`, ainsi qu’un événement `ItemSelected`. Les éléments affichés par la classe `MySpinner` sont fournis par le [`Adapter`](xref:Android.Widget.Adapter) associé à la vue, et les éléments sont remplis dans le `Adapter` lorsque la propriété `ItemsSource` est définie pour la première fois. Chaque fois que l’élément sélectionné dans la classe `MySpinner` change, le gestionnaire d’événements `OnBindableSpinnerItemSelected` met à jour la propriété `SelectedObject`.

## <a name="summary"></a>Résumé

Cet article a montré comment utiliser des vues natives à partir de fichiers de XAML de Xamarin.Forms. Propriétés et gestionnaires d’événements peuvent être définies sur les vues natives, et ils peuvent interagir avec les vues Xamarin.Forms.

## <a name="related-links"></a>Liens connexes

- [NativeSwitch (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Formulaires natifs](~/xamarin-forms/platform/native-forms.md)
- [Passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md)
