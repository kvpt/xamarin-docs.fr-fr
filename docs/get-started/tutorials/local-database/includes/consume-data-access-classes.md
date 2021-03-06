---
ms.openlocfilehash: 9e78d92bdd2d6b0b398ef30ba5f30f71ef64cfd3
ms.sourcegitcommit: b75c369adb8e02a429b6c0fed8ba4a855099bf01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557167"
---
Dans cet exercice, vous allez créer une interface utilisateur pour utiliser les classes d’accès aux données créées précédemment.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

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
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Age}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend deux instances [`Entry`](xref:Xamarin.Forms.Entry), un [`Button`](xref:Xamarin.Forms.Button) et un [`CollectionView`](xref:Xamarin.Forms.CollectionView), dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Chaque `Entry` a son propre jeu de propriétés [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder), lequel spécifie le texte d’espace réservé qui s’affiche avant une saisie de l’utilisateur. Le code `Button` définit son événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sur un gestionnaire d’événements nommé `OnButtonClicked` qui sera créé à l’étape suivante. La `CollectionView` définit sa propriété [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui utilise un `StackLayout` et deux objets [`Label`](xref:Xamarin.Forms.Label) pour définir l’apparence de chaque ligne dans la `CollectionView`. Les objets `Label` lient leurs propriétés `Text` aux propriétés `Name` et `Age` de chaque objet `Person`, respectivement.

    En outre, les instances [`Entry`](xref:Xamarin.Forms.Entry) et [`CollectionView`](xref:Xamarin.Forms.CollectionView) ont des noms spécifiés par l’attribut `x:Name`. Le fichier code-behind peut ainsi accéder à ces objets en utilisant les noms affectés.

1. Dans **l’Explorateur de solutions**, dans le projet **LocalDatabaseTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez l’attribut de remplacement `OnAppearing` et le gestionnaire d’événements `OnButtonClicked` à la classe :

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        collectionView.ItemsSource = await App.Database.GetPeopleAsync();
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
            collectionView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    La méthode `OnAppearing` remplit [`CollectionView`](xref:Xamarin.Forms.CollectionView) avec toutes les données stockées dans la base de données. La méthode `OnButtonClicked`, qui s’exécute après avoir appuyé sur [`Button`](xref:Xamarin.Forms.Button), enregistre les données saisies dans la base de données avant d’effacer les deux instances [`Entry`](xref:Xamarin.Forms.Entry) et d’actualiser les données dans `CollectionView`.

    > [!NOTE]
    > Le remplacement de la méthode `OnAppearing` est exécuté une fois que [`ContentPage`](xref:Xamarin.Forms.ContentPage) est mis en page, juste avant qu’il soit visible. Il représente donc un emplacement idéal pour définir le contenu des affichages Xamarin.Forms.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix.

    Entrez plusieurs éléments de données, en appuyant sur [`Button`](xref:Xamarin.Forms.Button) pour chaque élément de données. Cette opération enregistrera les données dans la base de données et remplira de nouveau [`CollectionView`](xref:Xamarin.Forms.CollectionView) avec toutes les données de la base de données :

    [![Capture d’écran montrant la persistance des données dans une base de données SQLite.NET locale, sur iOS et Android](../images/consume-data-access-classes.png "Persistance des données dans une base de données locale")](../images/consume-data-access-classes-large.png#lightbox "Persistance des données dans une base de données locale")

    Pour plus d’informations sur les bases de données locales dans Xamarin.Forms, consultez [Bases de données locales Xamarin.Forms (guide)](~/xamarin-forms/data-cloud/data/databases.md)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

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
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Age}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend deux instances [`Entry`](xref:Xamarin.Forms.Entry), un [`Button`](xref:Xamarin.Forms.Button) et un [`CollectionView`](xref:Xamarin.Forms.CollectionView), dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Chaque `Entry` a son propre jeu de propriétés [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder), lequel spécifie le texte d’espace réservé qui s’affiche avant une saisie de l’utilisateur. Le code `Button` définit son événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sur un gestionnaire d’événements nommé `OnButtonClicked` qui sera créé à l’étape suivante. La `CollectionView` définit sa propriété [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui utilise un `StackLayout` et deux objets [`Label`](xref:Xamarin.Forms.Label) pour définir l’apparence de chaque ligne dans la `CollectionView`. Les objets `Label` lient leurs propriétés `Text` aux propriétés `Name` et `Age` de chaque objet `Person`, respectivement.

    En outre, les instances [`Entry`](xref:Xamarin.Forms.Entry) et [`ListView`](xref:Xamarin.Forms.ListView) ont des noms spécifiés par l’attribut `x:Name`. Le fichier code-behind peut ainsi accéder à ces objets en utilisant les noms affectés.

1. Dans **Panneau Solutions**, dans le projet **LocalDatabaseTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez l’attribut de remplacement `OnAppearing` et le gestionnaire d’événements `OnButtonClicked` à la classe :

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        collectionView.ItemsSource = await App.Database.GetPeopleAsync();
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
            collectionView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    La méthode `OnAppearing` remplit [`CollectionView`](xref:Xamarin.Forms.CollectionView) avec toutes les données stockées dans la base de données. La méthode `OnButtonClicked`, qui s’exécute après avoir appuyé sur [`Button`](xref:Xamarin.Forms.Button), enregistre les données saisies dans la base de données avant d’effacer les deux instances [`Entry`](xref:Xamarin.Forms.Entry) et d’actualiser les données dans `CollectionView`.

    > [!NOTE]
    > Le remplacement de la méthode `OnAppearing` est exécuté une fois que [`ContentPage`](xref:Xamarin.Forms.ContentPage) est mis en page, juste avant qu’il soit visible. Il représente donc un emplacement idéal pour définir le contenu des affichages Xamarin.Forms.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix.

    Entrez plusieurs éléments de données, en appuyant sur [`Button`](xref:Xamarin.Forms.Button) pour chaque élément de données. Cette opération enregistrera les données dans la base de données et remplira de nouveau [`CollectionView`](xref:Xamarin.Forms.CollectionView) avec toutes les données de la base de données :

    [![Capture d’écran montrant la persistance des données dans une base de données SQLite.NET locale, sur iOS et Android](../images/consume-data-access-classes.png "Persistance des données dans une base de données locale")](../images/consume-data-access-classes-large.png#lightbox "Persistance des données dans une base de données locale")

    Pour plus d’informations sur les bases de données locales dans Xamarin.Forms, consultez [Bases de données locales Xamarin.Forms (guide)](~/xamarin-forms/data-cloud/data/databases.md)
