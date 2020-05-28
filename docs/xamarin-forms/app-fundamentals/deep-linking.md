---
title: ''
description: Cet article explique comment utiliser l’indexation d’application et la liaison profonde pour faciliter la Xamarin.Forms recherche de contenu d’application sur les appareils iOS et Android.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d93f9bbcfafc3cb71d6b71159f6d3368f50c08be
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135731"
---
# <a name="application-indexing-and-deep-linking"></a>Indexation d’applications et liens ciblés

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)

_L’indexation des applications permet aux applications qui seraient autrement oubliées après quelques utilisations de rester pertinentes en apparaissant dans les résultats de la recherche. La liaison profonde permet aux applications de répondre à un résultat de recherche qui contient des données d’application, généralement en accédant à une page référencée à partir d’un lien profond. Cet article explique comment utiliser l’indexation d’application et la liaison profonde pour faciliter la Xamarin.Forms recherche de contenu d’application sur les appareils iOS et Android._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Liaison profonde avec Xamarin.Forms et vidéo Azure**

Xamarin.Formsl’indexation d’application et la liaison profonde fournissent une API pour la publication de métadonnées pour l’indexation d’application lorsque les utilisateurs parcourent des applications. Le contenu indexé peut alors être recherché dans Spotlight Search, dans Google Search ou dans une recherche web. Le fait d’appuyer sur un résultat de recherche qui contient un lien ciblé déclenche un événement qui peut être géré par une application et qui est généralement utilisé pour accéder à la page référencée à partir du lien ciblé.

Cet exemple d’application montre une application de liste Todo où les données sont stockées dans une base de données SQLite locale, comme illustré dans les captures d’écran suivantes :

![](deep-linking-images/screenshots.png "TodoList Application")

Chaque instance `TodoItem` créée par l’utilisateur est indexée. La recherche propre à la plateforme peut ensuite servir à localiser les données indexées de l’application. Quand l’utilisateur appuie sur un élément de résultat de recherche de l’application, l’application démarre, la `TodoItemPage` est parcourue et le `TodoItem` référencé à partir du lien ciblé s’affiche.

Pour plus d’informations sur l’utilisation d’une base de données SQLite, consultez [ Xamarin.Forms bases de données locales](~/xamarin-forms/data-cloud/data/databases.md).

> [!NOTE]
> Xamarin.Formsl’indexation des applications et la fonctionnalité de liaison profonde sont uniquement disponibles sur les plateformes iOS et Android, et nécessitent au minimum iOS 9 et l’API 23 respectivement.

## <a name="setup"></a>Installation

Les sections suivantes fournissent des instructions d’installation supplémentaires pour utiliser cette fonctionnalité sur les plateformes iOS et Android.

### <a name="ios"></a>iOS

Sur la plateforme iOS, vérifiez que votre projet de plateforme iOS définit le fichier **Entitlements.plist** comme fichier de droits personnalisé pour signer le bundle.

Pour utiliser les liens universels iOS :

1. Ajoutez un droit Domaines associés à votre application, avec la clé `applinks`, qui inclut tous les domaines que votre application prendra en charge.
1. Ajoutez un fichier Apple App Site Association à votre site web.
1. Ajoutez la clé `applinks` dans le fichier Apple App Site Association.

Pour plus d’informations, consultez [Allowing Apps and Websites to Link to Your Content](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content) sur developer.apple.com.

### <a name="android"></a>Téléphone

Sur la plateforme Android, vous devez répondre à un nombre de prérequis pour utiliser la fonctionnalité d’indexation d’applications et de liens ciblés :

1. Une version de votre application doit être publiée sur Google Play.
1. Un site web d’accompagnement doit être inscrit auprès de l’application dans la console de développeur de Google. Une fois que l’application est associée à un site web, les URL fonctionnant à la fois pour le site web et l’application peuvent être indexées, puis être prises en compte dans les résultats de recherche. Pour plus d’informations, consultez [App Indexing on Google Search](https://support.google.com/googleplay/android-developer/answer/6041489) sur le site web de Google.
1. Votre application doit prendre en charge les intentions d’URL HTTP sur la classe `MainActivity`, qui indiquent à l’indexation d’applications les types de schémas de données d’URL auxquels l’application peut répondre. Pour plus d’informations, consultez [Configuring the Intent Filter](~/android/platform/app-linking.md#configure-intent-filter).

Une fois ces conditions préalables remplies, la configuration supplémentaire suivante est requise pour utiliser Xamarin.Forms l’indexation d’application et la liaison profonde sur la plateforme Android :

1. Installez le [ Xamarin.Forms . AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) le package NuGet dans le projet d’application Android.
1. Dans le fichier **MainActivity.cs**, ajoutez une déclaration pour utiliser l’espace de noms `Xamarin.Forms.Platform.Android.AppLinks`.
1. Dans le fichier **MainActivity.cs**, ajoutez une déclaration pour utiliser l’espace de noms `Firebase`.
1. Dans un navigateur web, créez un projet via la [console Firebase](https://console.firebase.google.com/).
1. Dans la console Firebase, ajoutez Firebase à votre application Android et entrez les données nécessaires.
1. Téléchargez le fichier **google-services.json** qui en résulte.
1. Ajoutez le fichier **google-services.json** dans le répertoire racine du projet Android et définissez son **Action de génération** sur **GoogleServicesJson**.
1. Dans l’élément de remplacement `MainActivity.OnCreate`, ajoutez la ligne suivante de code sous `Forms.Init(this, bundle)` :

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

Une fois que **google-services.json** est ajouté au projet (et que l’action de génération *GoogleServicesJson** est définie), le processus de génération extrait l’ID client et la clé API, puis ajoute ces informations d’identification au fichier manifeste généré.

> [!NOTE]
> Dans cet article, les termes « lien d’application » et « lien ciblé » sont souvent utilisés de manière interchangeable. Toutefois, ils ont des significations distinctes sur Android : un lien ciblé est un filtre d’intention qui permet aux utilisateurs d’entrer directement une activité spécifique dans l’application. Un lien ciblé peut ouvrir une boîte de dialogue de désambiguïsation, qui permet à l’utilisateur de sélectionner l’une des nombreuses applications capables de gérer l’URL. Un lien d’application Android est un lien ciblé basé sur l’URL de votre site web, et dont l’appartenance à ce site a été vérifiée. Si vous cliquez sur un lien d’application, votre application s’ouvre si elle est installée, sans ouvrir de boîte de dialogue de désambiguïsation.

Pour plus d’informations, consultez [contenu de lien profond avec Xamarin.Forms navigation](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) dans les URL sur le blog Xamarin.

## <a name="indexing-a-page"></a>Indexation d’une page

Le processus d’indexation d’une page et son exposition à la recherche Google et Spotlight est le suivant :

1. Créez un [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) qui contient les métadonnées requises pour indexer la page, ainsi qu’un lien profond pour revenir à la page lorsque l’utilisateur sélectionne le contenu indexé dans les résultats de la recherche.
1. Inscrivez l' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) instance pour l’indexer pour la recherche.

L’exemple de code suivant montre comment créer une [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) instance :

```csharp
AppLinkEntry GetAppLink(TodoItem item)
{
    var pageType = GetType().ToString();
    var pageLink = new AppLinkEntry
    {
        Title = item.Name,
        Description = item.Notes,
        AppLinkUri = new Uri($"http://{App.AppName}/{pageType}?id={item.ID}", UriKind.RelativeOrAbsolute),
        IsLinkActive = true,
        Thumbnail = ImageSource.FromFile("monkey.png")
    };

    pageLink.KeyValues.Add("contentType", "TodoItemPage");
    pageLink.KeyValues.Add("appName", App.AppName);
    pageLink.KeyValues.Add("companyName", "Xamarin");

    return pageLink;
}
```

L' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) instance contient un certain nombre de propriétés dont les valeurs sont requises pour indexer la page et créer un lien profond. Les [`Title`](xref:Xamarin.Forms.IAppLinkEntry.Title) [`Description`](xref:Xamarin.Forms.IAppLinkEntry.Description) Propriétés, et [`Thumbnail`](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) sont utilisées pour identifier le contenu indexé lorsqu’il apparaît dans les résultats de la recherche. La [`IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propriété a la valeur `true` pour indiquer que le contenu indexé est actuellement affiché. La [`AppLinkUri`](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) propriété est un `Uri` qui contient les informations requises pour revenir à la page actuelle et afficher le actuel `TodoItem` . L’exemple suivant montre un exemple d’`Uri` pour l’exemple d’application :

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

Cet `Uri` contient toutes les informations nécessaires pour lancer l’application `deeplinking`, parcourir la `DeepLinking.TodoItemPage` et afficher le `TodoItem` qui a un `ID` sur 2.

## <a name="registering-content-for-indexing"></a>Enregistrement du contenu pour l’indexation

Une fois qu’une [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) instance a été créée, elle doit être inscrite pour que l’indexation apparaisse dans les résultats de la recherche. Pour ce faire, vous devez utiliser [ `RegisterLink` ] (XREF : Xamarin.Forms . IAppLinks. RegisterLink ( Xamarin.Forms . IAppLinkEntry)), comme illustré dans l’exemple de code suivant :

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

L' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) instance est ajoutée à la collection de l’application [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) .

> [!NOTE]
> Vous pouvez aussi utiliser la méthode `RegisterLink` pour mettre à jour le contenu qui a été indexé pour une page.

Une fois qu’une [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) instance a été inscrite pour l’indexation, elle peut apparaître dans les résultats de la recherche. La capture d’écran suivante montre le contenu indexé apparaître dans les résultats de recherche sur la plateforme iOS :

![](deep-linking-images/ios-search.png "Indexed Content in Search Results on iOS")

## <a name="de-registering-indexed-content"></a>Désenregistrement du contenu indexé

[ `DeregisterLink` ] (XREF : Xamarin.Forms . IAppLinks. DeregisterLink ( Xamarin.Forms . IAppLinkEntry)) est utilisé pour supprimer le contenu indexé des résultats de la recherche, comme illustré dans l’exemple de code suivant :

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

Cela supprime l' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) instance de la collection de l’application [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) .

> [!NOTE]
> Sur Android, il n’est pas possible de supprimer le contenu indexé des résultats de recherche.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Réponse à un lien ciblé

Quand le contenu indexé apparaît dans les résultats de recherche et est sélectionné par l’utilisateur, la classe `App` de l’application reçoit une demande pour gérer l’`Uri` qui se trouve dans le contenu indexé. Cette requête peut être traitée dans le [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) remplacement, comme illustré dans l’exemple de code suivant :

```csharp
public class App : Application
{
    ...
    protected override async void OnAppLinkRequestReceived(Uri uri)
    {
        string appDomain = "http://" + App.AppName.ToLowerInvariant() + "/";
        if (!uri.ToString().ToLowerInvariant().StartsWith(appDomain, StringComparison.Ordinal))
            return;

        string pageUrl = uri.ToString().Replace(appDomain, string.Empty).Trim();
        var parts = pageUrl.Split('?');
        string page = parts[0];
        string pageParameter = parts[1].Replace("id=", string.Empty);

        var formsPage = Activator.CreateInstance(Type.GetType(page));
        var todoItemPage = formsPage as TodoItemPage;
        if (todoItemPage != null)
        {
            var todoItem = await App.Database.GetItemAsync(int.Parse(pageParameter));
            todoItemPage.BindingContext = todoItem;
            await MainPage.Navigation.PushAsync(formsPage as Page);
        }
        base.OnAppLinkRequestReceived(uri);
    }
}
```

La [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) méthode vérifie que le reçu `Uri` est destiné à l’application, avant d’analyser `Uri` dans la page à atteindre et le paramètre à passer à la page. Une instance de la page à parcourir est créée et le `TodoItem` représenté par le paramètre de page est récupéré. Le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page vers laquelle la navigation est définie est ensuite défini sur `TodoItem` . Cela garantit que lorsque le `TodoItemPage` est affiché par [ `PushAsync` ] (XREF : Xamarin.Forms . INavigation. PushAsync ( Xamarin.Forms . Page)), il affichera le `TodoItem` dont `ID` est contenu dans le lien profond.

## <a name="making-content-available-for-search-indexing"></a>Mise à disposition du contenu pour l’indexation de recherche

Chaque fois que la page représentée par un lien profond est affichée, la [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propriété peut avoir la `true` valeur. Sur iOS et Android, cela rend l' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) instance disponible pour l’indexation de la recherche, et sur iOS uniquement, elle rend également l' `AppLinkEntry` instance disponible pour la remise. Pour plus d’informations sur Handoff, consultez [Introduction à Handoff](~/ios/platform/handoff.md).

L’exemple de code suivant montre comment affecter à la propriété la valeur [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) `true` dans la [`Page.OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substitution :

```csharp
protected override void OnAppearing()
{
    appLink = GetAppLink(BindingContext as TodoItem);
    if (appLink != null)
    {
        appLink.IsLinkActive = true;
    }
}
```

De même, lorsque la page représentée par un lien profond est quittée, la [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propriété peut avoir la valeur `false` . Sur iOS et Android, cela arrête l' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) instance qui est publiée pour l’indexation de recherche et, sur iOS uniquement, elle arrête également la publication `AppLinkEntry` de l’instance pour la remise. Cela peut être accompli dans le [`Page.OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) remplacement, comme illustré dans l’exemple de code suivant :

```csharp
protected override void OnDisappearing()
{
    if (appLink != null)
    {
        appLink.IsLinkActive = false;
    }
}
```

## <a name="providing-data-to-handoff"></a>Ajout de données dans Handoff

Sur iOS, les données spécifiques à l’application peuvent être stockées lors de l’indexation de la page. Pour ce faire, vous devez ajouter des données à la [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) collection, qui est un `Dictionary<string, string>` pour le stockage des paires clé-valeur utilisées dans le transfert. Handoff est un moyen pour l’utilisateur de démarrer une activité sur l’un de ses appareils et de continuer cette activité sur un autre de ses appareils (identifié par le compte iCloud de l’utilisateur). Le code suivant montre un exemple de stockage de paires clé-valeur spécifiques à l’application :

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

Les valeurs stockées dans la [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) collection seront stockées dans les métadonnées de la page indexée et seront restaurées lorsque l’utilisateur appuie sur un résultat de recherche qui contient un lien profond (ou lorsque le transfert est utilisé pour afficher le contenu sur un autre appareil connecté).

De plus, vous pouvez spécifier les valeurs des clés suivantes :

- `contentType` : `string` qui spécifie l’URI du contenu indexé. La convention recommandée à utiliser pour cette valeur est le nom de type de la page contenant le contenu indexé.
- `associatedWebPage` : `string` qui représente la page web à visiter si le contenu indexé peut également être affiché sur le web, ou si l’application prend en charge les liens ciblés de Safari.
- `shouldAddToPublicIndex` : `string``true` ou `false` qui contrôle s’il faut ou non ajouter le contenu indexé dans l’index du cloud public d’Apple, qui peut après être présenté aux utilisateurs qui n’ont pas installé l’application sur leur appareil iOS. Toutefois, ce n’est pas parce que le contenu a été défini pour l’indexation publique que cela signifie qu’il sera ajouté automatiquement à l’index du cloud public d’Apple. Pour plus d’informations, consultez [Indexation de la recherche publique](~/ios/platform/search/nsuseractivity.md). Notez que cette clé doit être définie sur `false` lors de l’ajout de données personnelles à la [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) collection.

> [!NOTE]
> La collection `KeyValues` n’est pas utilisée sur la plateforme Android.

Pour plus d’informations sur Handoff, consultez [Introduction à Handoff](~/ios/platform/handoff.md).

## <a name="summary"></a>Résumé

Cet article a expliqué comment utiliser l’indexation d’application et la liaison profonde pour faciliter la Xamarin.Forms recherche de contenu d’application sur les appareils iOS et Android. L’indexation d’applications permet aux applications de rester pertinentes en apparaissant dans les résultats de recherche, qui sinon auraient été oubliées après quelques utilisations.

## <a name="related-links"></a>Liens connexes

- [Deep Linking (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)
- [API de recherche iOS](~/ios/platform/search/index.md)
- [Liaison d’applications dans Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
