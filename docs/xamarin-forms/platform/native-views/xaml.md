---
title : "affichages natifs en XAML" Description : "les affichages natifs d’iOS, Android et le plateforme Windows universelle peuvent être directement référencés à partir de Xamarin.Forms fichiers XAML. Les propriétés et les gestionnaires d’événements peuvent être définis sur des vues natives et interagir avec les Xamarin.Forms vues. Cet article montre comment utiliser des vues natives à partir de Xamarin.Forms fichiers XAML.
ms. Prod : xamarin ms. AssetID : 7A856D31-B300-409E-9AEB-F8A4DB99B37E ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 03/23/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="native-views-in-xaml"></a>Vues natives en XAML

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_Les affichages natifs d’iOS, Android et le plateforme Windows universelle peuvent être directement référencés à partir de Xamarin.Forms fichiers XAML. Les propriétés et les gestionnaires d’événements peuvent être définis sur des vues natives et interagir avec les Xamarin.Forms vues. Cet article montre comment utiliser des vues natives à partir de Xamarin.Forms fichiers XAML._

Pour incorporer une vue native dans un Xamarin.Forms fichier XAML :

1. Ajoutez une `xmlns` déclaration d’espace de noms dans le fichier XAML pour l’espace de noms qui contient la vue native.
1. Créez une instance de la vue native dans le fichier XAML.

> [!IMPORTANT]
> Le code XAML compilé doit être désactivé pour les pages XAML qui utilisent des vues natives. Pour ce faire, vous pouvez décorer la classe code-behind de votre page XAML avec l' `[XamlCompilation(XamlCompilationOptions.Skip)]` attribut. Pour plus d’informations sur la compilation XAML, consultez [compilation Xamarin.Forms XAML dans ](~/xamarin-forms/xaml/xamlc.md).

Pour référencer une vue native à partir d’un fichier code-behind, vous devez utiliser un projet de ressources partagées (SAP) et encapsuler le code spécifique à la plateforme avec des directives de compilation conditionnelles. Pour plus d’informations, consultez [faire référence aux affichages natifs à partir du code](#refer-to-native-views-from-code).

## <a name="consume-native-views"></a>Utiliser des vues natives

L’exemple de code suivant illustre l’utilisation de vues natives pour chaque plateforme sur un Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

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

En plus de spécifier `clr-namespace` et `assembly` pour un espace de noms de vue native, un `targetPlatform` doit également être spécifié. La valeur doit être,,, `iOS` `Android` (ce `UWP` `Windows` qui équivaut à `UWP` ),,, `macOS` `GTK` `Tizen` ou `WPF` . Au moment de l’exécution, l’analyseur XAML ignore tous les préfixes d’espaces de noms XML qui `targetPlatform` ne correspondent pas à la plateforme sur laquelle l’application s’exécute.

Chaque déclaration d’espace de noms peut être utilisée pour référencer une classe ou une structure à partir de l’espace de noms spécifié. Par exemple, la `ios` déclaration d’espace de noms peut être utilisée pour référencer une classe ou une structure à partir de l' `UIKit` espace de noms iOS. Les propriétés de la vue Native peuvent être définies via XAML, mais les types de propriété et d’objet doivent correspondre. Par exemple, la `UILabel.TextColor` propriété a la valeur à `UIColor.Red` l’aide de l' `x:Static` extension de balisage et de l' `ios` espace de noms.

Les propriétés pouvant être liées et les propriétés pouvant être liées peuvent également être définies sur des vues natives à l’aide de la `Class.BindableProperty="value"` syntaxe. Chaque vue native est encapsulée dans une instance spécifique à `NativeViewWrapper` la plateforme, qui dérive de la [`Xamarin.Forms.View`](xref:Xamarin.Forms.View) classe. Si vous définissez une propriété pouvant être liée ou une propriété pouvant être liée dans une vue native, la valeur de la propriété est transférée dans le wrapper. Par exemple, une disposition horizontale centrée peut être spécifiée en définissant `View.HorizontalOptions="Center"` sur la vue native.

> [!NOTE]
> Notez que les styles ne peuvent pas être utilisés avec les vues natives, car les styles peuvent uniquement cibler des propriétés qui sont soutenus par des `BindableProperty` objets.

Les constructeurs Android widget requièrent généralement l' `Context` objet Android comme argument, ce qui peut être rendu disponible par le biais d’une propriété statique dans la `MainActivity` classe. Par conséquent, lors de la création d’un widget Android en XAML, l' `Context` objet doit généralement être passé au constructeur du widget à l’aide de l' `x:Arguments` attribut avec une `x:Static` extension de balisage. Pour plus d’informations, consultez [passer des arguments à des vues natives](#pass-arguments-to-native-views).

> [!NOTE]
> Notez que l’attribution d’un nom à une vue native avec `x:Name` n’est pas possible dans un projet de bibliothèque .NET standard ou un projet de ressource partagé (SAP). Cela génère une variable du type natif, ce qui entraîne une erreur de compilation. Toutefois, les vues natives peuvent être encapsulées dans des `ContentView` instances et récupérées dans le fichier code-behind, à condition qu’un SAP soit utilisé. Pour plus d’informations, consultez [reportez-vous à la vue native à partir du code](#refer-to-native-views-from-code).

## <a name="native-bindings"></a>Liaisons natives

La liaison de données est utilisée pour synchroniser une interface utilisateur avec sa source de données et simplifie la façon dont une Xamarin.Forms application s’affiche et interagit avec ses données. À condition que l’objet source implémente l' `INotifyPropertyChanged` interface, les modifications apportées à l’objet *source* sont automatiquement envoyées à l’objet *cible* par l’infrastructure de liaison, et les modifications apportées à l’objet *cible* peuvent éventuellement faire l’objet d’un push vers l’objet *source* .

Les propriétés des vues natives peuvent également utiliser la liaison de données. L’exemple de code suivant illustre la liaison de données à l’aide des propriétés des vues natives :

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

La page contient un [`Entry`](xref:Xamarin.Forms.Entry) dont la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propriété est liée à la `NativeSwitchPageViewModel.IsSwitchOn` propriété. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page est définie sur une nouvelle instance de la `NativeSwitchPageViewModel` classe dans le fichier code-behind, avec la classe ViewModel qui implémente l' `INotifyPropertyChanged` interface.

La page contient également un commutateur natif pour chaque plateforme. Chaque commutateur natif utilise une [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) liaison pour mettre à jour la valeur de la `NativeSwitchPageViewModel.IsSwitchOn` propriété. Par conséquent, lorsque le commutateur est désactivé, `Entry` est désactivé et, lorsque le commutateur est activé, `Entry` est activé. Les captures d’écran suivantes illustrent cette fonctionnalité sur chaque plateforme :

![](xaml-images/native-switch-disabled.png "Native Switch Disabled")
![](xaml-images/native-switch-enabled.png "Native Switch Enabled")

Les liaisons bidirectionnelles sont prises en charge automatiquement, à condition que la propriété native implémente `INotifyPropertyChanged` ou prenne en charge l’observation de la valeur de clé (KVO) sur iOS, ou qu’elle soit `DependencyProperty` sur UWP. Toutefois, de nombreuses vues natives ne prennent pas en charge la notification de modification de propriété. Pour ces vues, vous pouvez spécifier une [`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName) valeur de propriété dans le cadre de l’expression de liaison. Cette propriété doit être définie sur le nom d’un événement dans la vue native qui signale quand la propriété cible a été modifiée. Ensuite, lorsque la valeur du commutateur natif change, la `Binding` classe est informée que l’utilisateur a modifié la valeur du commutateur et que la valeur de la `NativeSwitchPageViewModel.IsSwitchOn` propriété est mise à jour.

## <a name="pass-arguments-to-native-views"></a>Passer des arguments à des vues natives

Les arguments de constructeur peuvent être passés à des vues natives à l’aide de l' `x:Arguments` attribut avec une `x:Static` extension de balisage. En outre, les méthodes de fabrique d’affichage natives ( `public static` méthodes qui retournent des objets ou des valeurs du même type que la classe ou la structure qui définit les méthodes) peuvent être appelées en spécifiant le nom de la méthode à l’aide de l' `x:FactoryMethod` attribut et de ses arguments à l’aide de l' `x:Arguments` attribut.

L’exemple de code suivant illustre les deux techniques :

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

La [`UIFont.FromName`](xref:UIKit.UIFont.FromName*) méthode de fabrique est utilisée pour affecter [`UILabel.Font`](xref:UIKit.UILabel.Font) une nouvelle valeur à la propriété [`UIFont`](xref:UIKit.UIFont) sur iOS. Le `UIFont` nom et la taille sont spécifiés par les arguments de méthode qui sont des enfants de l' `x:Arguments` attribut.

La [`Typeface.Create`](xref:Android.Graphics.Typeface.Create*) méthode de fabrique est utilisée pour définir la [`TextView.Typeface`](xref:Android.Widget.TextView.Typeface) propriété sur un nouveau [`Typeface`](xref:Android.Graphics.Typeface) sur Android. Le `Typeface` nom et le style de famille sont spécifiés par les arguments de méthode qui sont des enfants de l' `x:Arguments` attribut.

Le [`FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) constructeur est utilisé pour définir la [`TextBlock.FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) propriété sur un nouveau `FontFamily` plateforme Windows universelle (UWP). Le `FontFamily` nom est spécifié par l’argument de méthode qui est un enfant de l' `x:Arguments` attribut.

> [!NOTE]
> Les arguments doivent correspondre aux types requis par le constructeur ou la méthode de fabrique.

Les captures d’écran suivantes montrent le résultat de la spécification de la méthode de fabrique et des arguments du constructeur pour définir la police sur différentes vues natives :

![](xaml-images/passing-arguments.png "Setting Fonts on Native Views")

Pour plus d’informations sur le passage d’arguments en XAML, consultez [passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md).

## <a name="refer-to-native-views-from-code"></a>Faire référence à des affichages natifs à partir du code

Bien qu’il ne soit pas possible de nommer une vue native avec l' `x:Name` attribut, il est possible de récupérer une instance de vue Native déclarée dans un fichier XAML à partir de son fichier code-behind dans un projet Access partagé, à condition que la vue Native soit un enfant d’un [`ContentView`](xref:Xamarin.Forms.ContentView) qui spécifie une `x:Name` valeur d’attribut. Ensuite, à l’intérieur des directives de compilation conditionnelle dans le fichier code-behind, vous devez :

1. Récupérez la valeur de la [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) propriété et effectuez un cast de celle-ci en un type spécifique à la plateforme `NativeViewWrapper` .
1. Récupérez la `NativeViewWrapper.NativeElement` propriété et effectuez un cast de celle-ci en type d’affichage natif.

L’API native peut ensuite être appelée sur la vue native pour effectuer les opérations souhaitées. Cette approche offre également l’avantage que plusieurs vues natives XAML pour différentes plateformes peuvent être des enfants du même [`ContentView`](xref:Xamarin.Forms.ContentView) . L’exemple de code suivant illustre cette technique :

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

Dans l’exemple ci-dessus, les vues natives pour chaque plateforme sont des enfants de [`ContentView`](xref:Xamarin.Forms.ContentView) contrôles, avec la `x:Name` valeur d’attribut utilisée pour récupérer `ContentView` dans le code-behind :

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

La [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) propriété est accessible pour récupérer la vue Native encapsulée en tant qu’instance spécifique à la plateforme `NativeViewWrapper` . La `NativeViewWrapper.NativeElement` propriété est ensuite accessible pour récupérer la vue Native comme son type natif. L’API de la vue native est ensuite appelée pour effectuer les opérations souhaitées.

Les boutons iOS et Android natifs partagent le même `OnButtonTap` Gestionnaire d’événements, car chaque bouton natif consomme un `EventHandler` délégué en réponse à un événement tactile. Toutefois, le plateforme Windows universelle (UWP) utilise un distinct `RoutedEventHandler` qui, à son tour, utilise le `OnButtonTap` Gestionnaire d’événements dans cet exemple. Par conséquent, lorsque l’utilisateur clique sur un bouton natif, le `OnButtonTap` Gestionnaire d’événements s’exécute, qui met à l’échelle et fait pivoter le contrôle natif contenu dans le [`ContentView`](xref:Xamarin.Forms.ContentView) nommé `contentViewTextParent` . Les captures d’écran suivantes montrent ce qui se produit sur chaque plateforme :

![](xaml-images/contentview.png "ContentView Containing a Native Control")

## <a name="subclass-native-views"></a>Sous-classes (vues natives)

De nombreuses vues natives iOS et Android ne sont pas adaptées à l’instanciation en XAML, car elles utilisent des méthodes, plutôt que des propriétés, pour configurer le contrôle. La solution à ce problème consiste à sous-créer des vues natives dans des wrappers qui définissent une API XAML plus conviviale qui utilise des propriétés pour configurer le contrôle et qui utilise des événements indépendants de la plateforme. Les vues natives encapsulées peuvent ensuite être placées dans un projet de ressources partagées (SAP) et entourées de directives de compilation conditionnelle, ou placées dans des projets spécifiques à la plateforme et référencées à partir de XAML dans un projet de bibliothèque .NET Standard.

L’exemple de code suivant montre une Xamarin.Forms page qui utilise des vues natives sous-classées :

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

La page contient un [`Label`](xref:Xamarin.Forms.Label) qui affiche le fruit choisi par l’utilisateur à partir d’un contrôle natif. Est `Label` lié à la `SubclassedNativeControlsPageViewModel.SelectedFruit` propriété. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page est définie sur une nouvelle instance de la `SubclassedNativeControlsPageViewModel` classe dans le fichier code-behind, avec la classe ViewModel qui implémente l' `INotifyPropertyChanged` interface.

La page contient également une vue de sélecteur natif pour chaque plateforme. Chaque vue Native affiche la collection de fruits en liant sa `ItemSource` propriété à la `SubclassedNativeControlsPageViewModel.Fruits` collection. Cela permet à l’utilisateur de choisir un fruit, comme illustré dans les captures d’écran suivantes :

![](xaml-images/sub-classed.png "Subclassed Native Views")

Sur iOS et Android, les sélecteurs natifs utilisent des méthodes pour configurer les contrôles. Par conséquent, ces sélecteurs doivent être sous-classées pour exposer des propriétés afin de les rendre compatibles avec XAML. Sur la plateforme Windows universelle (UWP), le `ComboBox` est déjà compatible avec XAML et ne nécessite donc pas de sous-classement.

### <a name="ios"></a>iOS

L’implémentation iOS sous-classe la [`UIPickerView`](xref:UIKit.UIPickerView) vue et expose les propriétés et un événement qui peut être facilement consommé à partir de XAML :

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

La `MyUIPickerView` classe expose `ItemsSource` les `SelectedItem` Propriétés et, ainsi qu’un `SelectedItemChanged` événement. Un [`UIPickerView`](xref:UIKit.UIPickerView) requiert un modèle de données sous-jacent [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) , accessible par les `MyUIPickerView` Propriétés et l’événement. Le `UIPickerViewModel` modèle de données est fourni par la `PickerModel` classe :

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

La `PickerModel` classe fournit le stockage sous-jacent pour la `MyUIPickerView` classe, via la `Items` propriété. Chaque fois que l’élément sélectionné `MyUIPickerView` est modifié, la [`Selected`](xref:UIKit.UIPickerViewModel.Selected*) méthode est exécutée, ce qui met à jour l’index sélectionné et déclenche l' `ItemChanged` événement. Cela permet de s’assurer que la `SelectedItem` propriété retourne toujours le dernier élément choisi par l’utilisateur. En outre, la `PickerModel` classe substitue les méthodes utilisées pour configurer l’instance de `MyUIPickerView` .

### <a name="android"></a>Android

L’implémentation Android sous-classe la [`Spinner`](xref:Android.Widget.Spinner) vue et expose les propriétés et un événement qui peut être facilement consommé à partir de XAML :

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

La `MySpinner` classe expose `ItemsSource` les `SelectedObject` Propriétés et, ainsi qu’un `ItemSelected` événement. Les éléments affichés par la `MySpinner` classe sont fournis par le [`Adapter`](xref:Android.Widget.Adapter) associé à la vue, et les éléments sont remplis dans le `Adapter` lorsque la `ItemsSource` propriété est définie pour la première fois. Chaque fois que l’élément sélectionné dans la `MySpinner` classe change, le `OnBindableSpinnerItemSelected` Gestionnaire d’événements met à jour la `SelectedObject` propriété.

## <a name="related-links"></a>Liens connexes

- [NativeSwitch (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Formulaires natifs](~/xamarin-forms/platform/native-forms.md)
- [Passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md)
