---
ms.openlocfilehash: c92a97b336e89214bbd95021ad8fb9a56f64cc8c
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659800"
---
Dans cet exercice, vous créerez une interface utilisateur pour consommer la classe `RestService`, qui à son tour récupère les données de l’API web [OpenWeatherMap](https://openweathermap.org/).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans **l’Explorateur de solutions**, dans le projet **WebServiceTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend les éléments [`Entry`](xref:Xamarin.Forms.Entry), [`Button`](xref:Xamarin.Forms.Button) et une série d’instances [`Label`](xref:Xamarin.Forms.Label) dans [`Grid`](xref:Xamarin.Forms.Grid). L’élément `Entry` est prérempli avec « Seattle » en définissant sa propriété [`Text`](xref:Xamarin.Forms.Entry.Text). Le code `Button` définit son événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sur un gestionnaire d’événements nommé `OnButtonClicked` qui sera créé à l’étape suivante. La moitié des instances `Label` affichent du texte statique, tandis que les instances de données restantes sont liées aux propriétés `WeatherData`. Lors de l’exécution, les instances `Label` qui utilisent la liaison de données examineront leurs propriétés [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) respectives à la recherche de l’objet `WeatherData` à utiliser dans leurs expressions de liaison. Pour plus d’informations sur la liaison de données, consultez la page [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    En outre, l’élément [`Entry`](xref:Xamarin.Forms.Entry) dispose d’un nom spécifié avec l’attribut `x:Name`. Ainsi, le fichier code-behind peut accéder à l’objet en utilisant le nom affecté.

1. Dans **l’Explorateur de solutions**, dans le projet **WebServiceTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Ensuite, dans **MainPage.xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    La méthode `OnButtonClicked`, qui est exécutée lors d’un appui sur [`Button`](xref:Xamarin.Forms.Button), appelle la méthode `RestService.GetWeatherDataAsync` pour récupérer les données météorologiques de la ville dont le nom a été entré dans [`Entry`](xref:Xamarin.Forms.Entry). La méthode `GetWeatherDataAsync` requiert un argument `string` représentant l’URI de l’API web appelée et elle est générée par la méthode `GenerateRequestUri`. Cette méthode récupère l’adresse du point de terminaison stockée dans la constante `OpenWeatherMapEndpoint` et y ajoute des paramètres de requête spécifiant les éléments suivants :

    - La ville pour laquelle les données météorologiques sont requises.
    - Les unités de mesure des données météorologiques.
    - Votre clé API personnelle.

    Après avoir récupéré les données météorologiques demandées, l’élément [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page est défini sur l’objet `WeatherData`. Pour plus d’informations sur la propriété `BindingContext`, consultez la page [Liaisons avec un contexte de liaison](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) du guide [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > La propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) est héritée via l’arborescence d’éléments visuels. Par conséquent, comme elle a été définie sur l’objet [`ContentPage`](xref:Xamarin.Forms.ContentPage), les objets enfants de l’élément `ContentPage` héritent de sa valeur, y compris des instances [`Label`](xref:Xamarin.Forms.Label).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix. Appuyez sur [`Button`](xref:Xamarin.Forms.Button) pour récupérer les données météorologiques actuelles pour Seattle :

    [![Capture d’écran des données météorologiques de Seattle, sur iOS et Android](../images/consume-web-service.png "Données météorologiques de Seattle")](../images/consume-web-service-large.png#lightbox "Données météorologiques de Seattle")

    > [!IMPORTANT]
    > Vous devez définir votre clé API OpenWeatherMap personnelle comme valeur de la constante `OpenWeatherMapAPIKey` dans la classe `Constants`.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **Panneau Solutions**, dans le projet **WebServiceTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend les éléments [`Entry`](xref:Xamarin.Forms.Entry), [`Button`](xref:Xamarin.Forms.Button) et une série d’instances [`Label`](xref:Xamarin.Forms.Label) dans [`Grid`](xref:Xamarin.Forms.Grid). L’élément `Entry` est prérempli avec « Seattle » en définissant sa propriété [`Text`](xref:Xamarin.Forms.Entry.Text). Le code `Button` définit son événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sur un gestionnaire d’événements nommé `OnButtonClicked` qui sera créé à l’étape suivante. La moitié des instances `Label` affichent du texte statique, tandis que les instances de données restantes sont liées aux propriétés `WeatherData`. Lors de l’exécution, les instances `Label` qui utilisent la liaison de données examineront leurs propriétés [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) respectives à la recherche de l’objet `WeatherData` à utiliser dans leurs expressions de liaison. Pour plus d’informations sur la liaison de données, consultez la page [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    En outre, l’élément [`Entry`](xref:Xamarin.Forms.Entry) dispose d’un nom spécifié avec l’attribut `x:Name`. Ainsi, le fichier code-behind peut accéder à l’objet en utilisant le nom affecté.

    Pour plus d’informations sur l’utilisation des services web basés sur REST dans Xamarin.Forms, consultez [Utiliser un service web RESTful (guide)](~/xamarin-forms/data-cloud/web-services/rest.md).

1. Dans **Panneau Solutions**, dans le projet **WebServiceTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Ensuite, dans **MainPage.xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    La méthode `OnButtonClicked`, qui est exécutée lors d’un appui sur [`Button`](xref:Xamarin.Forms.Button), appelle la méthode `RestService.GetWeatherDataAsync` pour récupérer les données météorologiques de la ville dont le nom a été entré dans [`Entry`](xref:Xamarin.Forms.Entry). La méthode `GetWeatherDataAsync` requiert un argument `string` représentant l’URI de l’API web appelée et elle est générée par la méthode `GenerateRequestUri`. Cette méthode récupère l’adresse du point de terminaison stockée dans la constante `OpenWeatherMapEndpoint` et y ajoute des paramètres de requête spécifiant les éléments suivants :

    - La ville pour laquelle les données météorologiques sont requises.
    - Les unités de mesure des données météorologiques.
    - Votre clé API personnelle.

    Après avoir récupéré les données météorologiques demandées, l’élément [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page est défini sur l’objet `WeatherData`. Pour plus d’informations sur la propriété `BindingContext`, consultez la page [Liaisons avec un contexte de liaison](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) du guide [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > La propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) est héritée via l’arborescence d’éléments visuels. Par conséquent, comme elle a été définie sur l’objet [`ContentPage`](xref:Xamarin.Forms.ContentPage), les objets enfants de l’élément `ContentPage` héritent de sa valeur, y compris des instances [`Label`](xref:Xamarin.Forms.Label).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix. Appuyez sur [`Button`](xref:Xamarin.Forms.Button) pour récupérer les données météorologiques actuelles pour Seattle :

    [![Capture d’écran des données météorologiques de Seattle, sur iOS et Android](../images/consume-web-service.png "Données météorologiques de Seattle")](../images/consume-web-service-large.png#lightbox "Données météorologiques de Seattle")

    > [!IMPORTANT]
    > Vous devez définir votre clé API OpenWeatherMap personnelle comme valeur de la constante `OpenWeatherMapAPIKey` dans la classe `Constants`.

    Pour plus d’informations sur l’utilisation de services web basés sur REST dans Xamarin.Forms, consultez [Utiliser un service web RESTful (guide)](~/xamarin-forms/data-cloud/web-services/rest.md).
