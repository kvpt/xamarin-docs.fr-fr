---
ms.openlocfilehash: caee3eeda90a560f032c17657072ae5ba5023a69
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135162"
---
Dans cet exercice, vous allez créer une interface utilisateur pour utiliser les classes d’accès aux données créées précédemment.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans **l’Explorateur de solutions**, dans le projet **LocalDatabaseTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <ListView x:Name="listView">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}"
                                  Detail="{Binding Age}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend deux instances [`Entry`](xref:Xamarin.Forms.Entry), un [`Button`](xref:Xamarin.Forms.Button) et un [`ListView`](xref:Xamarin.Forms.ListView), dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Chaque `Entry` a son propre jeu de propriétés [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder), lequel spécifie le texte d’espace réservé qui s’affiche avant une saisie de l’utilisateur. Le code `Button` définit son événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sur un gestionnaire d’événements nommé `OnButtonClicked` qui sera créé à l’étape suivante. Le code `ListView` définit sa propriété [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) sur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui utilise [`TextCell`](xref:Xamarin.Forms.TextCell) pour définir l’apparence de chaque ligne dans [`ListView`](xref:Xamarin.Forms.ListView). Les données `TextCell` relient leurs propriétés [`Text`](xref:Xamarin.Forms.TextCell.Text) et [`Detail`](xref:Xamarin.Forms.TextCell.Detail) aux propriétés `Name` et `Age` de chaque objet `Person`, respectivement.

    En outre, les instances [`Entry`](xref:Xamarin.Forms.Entry) et [`ListView`](xref:Xamarin.Forms.ListView) ont des noms spécifiés par l’attribut `x:Name`. Le fichier code-behind peut ainsi accéder à ces objets en utilisant les noms affectés.

1. Dans **l’Explorateur de solutions**, dans le projet **LocalDatabaseTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez l’attribut de remplacement `OnAppearing` et le gestionnaire d’événements `OnButtonClicked` à la classe :

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        listView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            listView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    La méthode `OnAppearing` remplit [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les données stockées dans la base de données. La méthode `OnButtonClicked`, qui s’exécute après avoir appuyé sur [`Button`](xref:Xamarin.Forms.Button), enregistre les données saisies dans la base de données avant d’effacer les deux instances [`Entry`](xref:Xamarin.Forms.Entry) et d’actualiser les données dans `ListView`.

    > [!NOTE]
    > Le remplacement de la méthode `OnAppearing` est exécuté une fois que [`ContentPage`](xref:Xamarin.Forms.ContentPage) est mis en page, mais juste avant qu’il soit visible. Il représente donc un emplacement idéal pour définir le contenu des affichages Xamarin.Forms.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix.

    Entrez plusieurs éléments de données, en appuyant sur [`Button`](xref:Xamarin.Forms.Button) pour chaque élément de données. Cette opération enregistrera les données dans la base de données et remplira de nouveau [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les données de la base de données :

    [![Capture d’écran montrant la persistance des données dans une base de données SQLite.NET locale, sur iOS et Android](../images/consume-data-access-classes.png "Persistance des données dans une base de données locale")](../images/consume-data-access-classes-large.png#lightbox "Persistance des données dans une base de données locale")

    Pour plus d’informations sur les bases de données locales dans Xamarin.Forms, consultez [Bases de données locales Xamarin.Forms (guide)](~/xamarin-forms/data-cloud/data/databases.md)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **Panneau Solutions**, dans le projet **LocalDatabaseTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <ListView x:Name="listView">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}"
                                  Detail="{Binding Age}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend deux instances [`Entry`](xref:Xamarin.Forms.Entry), un [`Button`](xref:Xamarin.Forms.Button) et un [`ListView`](xref:Xamarin.Forms.ListView), dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Chaque `Entry` a son propre jeu de propriétés [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder), lequel spécifie le texte d’espace réservé qui s’affiche avant une saisie de l’utilisateur. Le code `Button` définit son événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sur un gestionnaire d’événements nommé `OnButtonClicked` qui sera créé à l’étape suivante. Le code `ListView` définit sa propriété [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) sur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui utilise [`TextCell`](xref:Xamarin.Forms.TextCell) pour définir l’apparence de chaque ligne dans [`ListView`](xref:Xamarin.Forms.ListView). Les données `TextCell` relient leurs propriétés [`Text`](xref:Xamarin.Forms.TextCell.Text) et [`Detail`](xref:Xamarin.Forms.TextCell.Detail) aux propriétés `Name` et `Age` de chaque objet `Person`, respectivement.

    En outre, les instances [`Entry`](xref:Xamarin.Forms.Entry) et [`ListView`](xref:Xamarin.Forms.ListView) ont des noms spécifiés par l’attribut `x:Name`. Le fichier code-behind peut ainsi accéder à ces objets en utilisant les noms affectés.

1. Dans **Panneau Solutions**, dans le projet **LocalDatabaseTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez l’attribut de remplacement `OnAppearing` et le gestionnaire d’événements `OnButtonClicked` à la classe :

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        listView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            listView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    La méthode `OnAppearing` remplit [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les données stockées dans la base de données. La méthode `OnButtonClicked`, qui s’exécute après avoir appuyé sur [`Button`](xref:Xamarin.Forms.Button), enregistre les données saisies dans la base de données avant d’effacer les deux instances [`Entry`](xref:Xamarin.Forms.Entry) et d’actualiser les données dans `ListView`.

    > [!NOTE]
    > Le remplacement de la méthode `OnAppearing` est exécuté une fois que [`ContentPage`](xref:Xamarin.Forms.ContentPage) est mis en page, mais juste avant qu’il soit visible. Il représente donc un emplacement idéal pour définir le contenu des affichages Xamarin.Forms.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix.

    Entrez plusieurs éléments de données, en appuyant sur [`Button`](xref:Xamarin.Forms.Button) pour chaque élément de données. Cette opération enregistrera les données dans la base de données et remplira de nouveau [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les données de la base de données :

    [![Capture d’écran montrant la persistance des données dans une base de données SQLite.NET locale, sur iOS et Android](../images/consume-data-access-classes.png "Persistance des données dans une base de données locale")](../images/consume-data-access-classes-large.png#lightbox "Persistance des données dans une base de données locale")

    Pour plus d’informations sur les bases de données locales dans Xamarin.Forms, consultez [Bases de données locales Xamarin.Forms (guide)](~/xamarin-forms/data-cloud/data/databases.md)
