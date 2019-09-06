---
title: Infrastructure sociale dans Xamarin. iOS
description: L’infrastructure sociale fournit une API unifiée pour l’interaction avec les réseaux sociaux, y compris Twitter et Facebook, ainsi que SinaWeibo pour les utilisateurs en Chine.
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: fd94cd7a6d37e7fa00489e788f232842b319e5d3
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292668"
---
# <a name="social-framework-in-xamarinios"></a>Infrastructure sociale dans Xamarin. iOS

_L’infrastructure sociale fournit une API unifiée pour l’interaction avec les réseaux sociaux, y compris Twitter et Facebook, ainsi que SinaWeibo pour les utilisateurs en Chine._

L’utilisation de l’infrastructure sociale permet aux applications d’interagir avec les réseaux sociaux à partir d’une seule API sans avoir à gérer l’authentification. Il comprend un contrôleur d’affichage fourni par le système pour la composition des publications, ainsi qu’une abstraction qui permet de consommer l’API de chaque réseau social sur HTTP.

> [!IMPORTANT]
> Pour qu’une API multiplateforme se connecte à différents réseaux sociaux, consultez le composant [Xamarin. social](http://components.xamarin.com/view/xamarin.social/) dans le magasin de composants Xamarin.

## <a name="connecting-to-twitter"></a>Connexion à Twitter

### <a name="twitter-account-settings"></a>Paramètres du compte Twitter

Pour vous connecter à Twitter à l’aide de l’infrastructure sociale, vous devez configurer un compte dans les paramètres de l’appareil, comme indiqué ci-dessous :

 [![](social-framework-images/twitter01.png "Paramètres du compte Twitter")](social-framework-images/twitter01.png#lightbox)

Une fois qu’un compte a été entré et vérifié avec Twitter, toute application sur l’appareil qui utilise les classes d’infrastructure sociale pour accéder à Twitter utilise ce compte.

### <a name="sending-tweets"></a>Envoi de tweets

L’infrastructure sociale comprend un contrôleur appelé `SLComposeViewController` qui présente une vue fournie par le système pour la modification et l’envoi d’un tweet. La capture d’écran suivante montre un exemple de cette vue :

 [![](social-framework-images/twitter02.png "Cette capture d’écran montre un exemple de SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

Pour utiliser un `SLComposeViewController` avec Twitter, vous devez créer une instance du contrôleur en appelant la `FromService` méthode avec `SLServiceType.Twitter` , comme indiqué ci-dessous :

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

Une fois `SLComposeViewController` l’instance retournée, elle peut être utilisée pour présenter une interface utilisateur pour la publication sur Twitter. Toutefois, la première chose à faire est de vérifier la disponibilité du réseau social, Twitter dans ce cas, en appelant `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController`n’envoie jamais de Tweet directement sans intervention de l’utilisateur. Toutefois, il peut être initialisé à l’aide des méthodes suivantes :

- `SetInitialText`: Ajoute le texte initial à afficher dans le tweet.
- `AddUrl`: Ajoute une URL au tweet.
- `AddImage`: Ajoute une image au tweet.


Une fois initialisé, l' `PresentVIewController` appel de affiche la vue créée `SLComposeViewController`par le. L’utilisateur peut ensuite éventuellement modifier et envoyer le tweet, ou annuler son envoi. Dans les deux cas, le contrôleur doit être rejeté dans `CompletionHandler`le, où le résultat peut également être vérifié pour voir si le tweet a été envoyé ou annulé, comme indiqué ci-dessous :

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>Exemple de Tweet

Le code suivant illustre l’utilisation `SLComposeViewController` du pour présenter une vue utilisée pour envoyer un tweet :

```csharp
using System;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _twitterComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
        #endregion

        #region Computed Properties
        public bool isTwitterAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Twitter); }
        }

        public SLComposeViewController TwitterComposer {
            get { return _twitterComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {

        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            SendTweet.Enabled = isTwitterAvailable;
        }
        #endregion

        #region Actions
        partial void SendTweet_TouchUpInside (UIButton sender)
        {
            // Set initial message
            TwitterComposer.SetInitialText ("Hello Twitter!");
            TwitterComposer.AddImage (UIImage.FromFile ("Icon.png"));
            TwitterComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (TwitterComposer, true, null);
        }
        #endregion
    }
}
```

### <a name="calling-twitter-api"></a>Appel de l’API Twitter

L’infrastructure sociale prend également en charge la création de requêtes HTTP sur des réseaux sociaux. Elle encapsule la requête dans une `SLRequest` classe qui est utilisée pour cibler l’API du réseau social particulier.

Par exemple, le code suivant effectue une demande à Twitter pour obtenir la chronologie publique (en développant le code indiqué ci-dessus) :

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _twitterAccount;
#endregion

#region Computed Properties
public ACAccount TwitterAccount {
    get { return _twitterAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    SendTweet.Enabled = isTwitterAvailable;
    RequestTwitterTimeline.Enabled = false;

    // Initialize Twitter Account access
    var accountStore = new ACAccountStore ();
    var accountType = accountStore.FindAccountType (ACAccountType.Twitter);

    // Request access to Twitter account
    accountStore.RequestAccess (accountType, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestTwitterTimeline.Enabled = true;
            });
        }
    });
}
#endregion

#region Actions
partial void RequestTwitterTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
    var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = TwitterAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

Examinons ce code en détail. Tout d’abord, il accède au magasin de comptes et obtient le type d’un compte Twitter :

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

Ensuite, il demande à l’utilisateur si votre application peut accéder à son compte Twitter et, si l’accès est accordé, le compte est chargé en mémoire et l’interface utilisateur est mise à jour :

```csharp
// Request access to Twitter account
accountStore.RequestAccess (accountType, (granted, error) => {
    // Allowed by user?
    if (granted) {
        // Get account
        _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
        InvokeOnMainThread (() => {
            // Update UI
            RequestTwitterTimeline.Enabled = true;
        });
    }
});
```

Lorsque l’utilisateur demande les données de chronologie (en appuyant sur un bouton dans l’interface utilisateur), l’application commence par faire une demande d’accès aux données à partir de Twitter :

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```

Cet exemple limite les résultats retournés aux dix dernières entrées en incluant `?count=10` dans l’URL. Enfin, il joint la requête au compte Twitter (qui a été chargé ci-dessus) et effectue l’appel à Twitter pour extraire les données :

```csharp
// Request data
request.Account = TwitterAccount;
request.PerformRequest ((data, response, error) => {
    // Was there an error?
    if (error == null) {
        // Was the request successful?
        if (response.StatusCode == 200) {
            // Yes, display it
            InvokeOnMainThread (() => {
                Results.Text = data.ToString ();
            });
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", response.StatusCode);
            });
        }
    } else {
        // No, display error
        InvokeOnMainThread (() => {
            Results.Text = string.Format ("Error: {0}", error);
        });
    }
});
```

Si les données ont été chargées avec succès, les données JSON brutes sont affichées (comme dans l’exemple de sortie ci-dessous) :

[![](social-framework-images/twitter03.png "Exemple d’affichage de données JSON brutes")](social-framework-images/twitter03.png#lightbox)

Dans une application réelle, les résultats JSON pouvaient ensuite être analysés normalement et les résultats présentés à l’utilisateur. Pour plus d’informations sur l’analyse de JSON, consultez [Introduction aux services Web](~/cross-platform/data-cloud/web-services/index.md) .

## <a name="connecting-to-facebook"></a>Connexion à Facebook

### <a name="facebook-account-settings"></a>Paramètres de compte Facebook

La connexion à Facebook à l’aide de l’infrastructure sociale est quasiment identique au processus utilisé pour Twitter présenté ci-dessus. Un compte d’utilisateur Facebook doit être configuré dans les paramètres de l’appareil, comme indiqué ci-dessous :

[![](social-framework-images/facebook01.png "Paramètres de compte Facebook")](social-framework-images/facebook01.png#lightbox)

Une fois configurée, toute application sur l’appareil qui utilise l’infrastructure sociale utilise ce compte pour se connecter à Facebook.

### <a name="posting-to-facebook"></a>Publication sur Facebook

Étant donné que l’infrastructure sociale est une API unifiée conçue pour accéder à plusieurs réseaux sociaux, le code reste quasiment identique quel que soit le réseau social utilisé.

Par exemple, le `SLComposeViewController` peut être utilisé exactement comme dans l’exemple Twitter présenté précédemment, la seule différence est le passage aux paramètres et options spécifiques à Facebook. Par exemple :

```csharp
using System;
using Foundation;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _facebookComposer = SLComposeViewController.FromService (SLServiceType.Facebook);
        #endregion

        #region Computed Properties
        public bool isFacebookAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Facebook); }
        }

        public SLComposeViewController FacebookComposer {
            get { return _facebookComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {

        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            PostToFacebook.Enabled = isFacebookAvailable;
        }
        #endregion

        #region Actions
        partial void PostToFacebook_TouchUpInside (UIButton sender)
        {
            // Set initial message
            FacebookComposer.SetInitialText ("Hello Facebook!");
            FacebookComposer.AddImage (UIImage.FromFile ("Icon.png"));
            FacebookComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (FacebookComposer, true, null);
        }
        #endregion
    }
}
```

Lorsqu’il est utilisé avec Facebook `SLComposeViewController` , le affiche une vue qui ressemble presque exactement à l’exemple Twitter, en affichant **Facebook** comme titre dans ce cas :

[![](social-framework-images/facebook02.png "Affichage SLComposeViewController")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>Appel de API Graph Facebook

Semblable à l’exemple Twitter, l’objet de `SLRequest` l’infrastructure sociale peut être utilisé avec l’API Graph de Facebook. Par exemple, le code suivant retourne des informations à partir de l’API Graph sur le compte Xamarin (en développant le code indiqué ci-dessus) :

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _facebookAccount;
#endregion

#region Computed Properties
public ACAccount FacebookAccount {
    get { return _facebookAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    PostToFacebook.Enabled = isFacebookAvailable;
    RequestFacebookTimeline.Enabled = false;

    // Initialize Facebook Account access
    var accountStore = new ACAccountStore ();
    var options = new AccountStoreOptions ();
    var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
    accountType = accountStore.FindAccountType (ACAccountType.Facebook);

    // Request access to Facebook account
    accountStore.RequestAccess (accountType, options, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _facebookAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestFacebookTimeline.Enabled = true;
            });
        }
    });

}
#endregion

#region Actions
partial void RequestFacebookTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl ("https://graph.facebook.com/283148898401104");
    var request = SLRequest.Create (SLServiceKind.Facebook, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = FacebookAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

La seule différence réelle entre ce code et la version Twitter présentée ci-dessus est que Facebook doit avoir un ID spécifique au développeur/à l’application (que vous pouvez générer à partir du portail des développeurs de Facebook), qui doit être défini comme option lors de la demande :

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

Si vous ne définissez pas cette option (ou si vous utilisez une clé non valide), une erreur se produit ou aucune donnée n’est retournée.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser l’infrastructure sociale pour interagir avec Twitter et Facebook. Il a montré comment configurer des comptes pour chaque réseau social dans les paramètres de l’appareil. Elle a également expliqué comment utiliser le `SLComposeViewController` pour présenter une vue unifiée pour la publication sur des réseaux sociaux. En outre, il a examiné `SLRequest` la classe utilisée pour appeler l’API de chaque réseau social.


## <a name="related-links"></a>Liens associés

- [SocialFrameworkDemo (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/socialframeworkdemo)
- [Introduction aux services web](~/cross-platform/data-cloud/web-services/index.md)
