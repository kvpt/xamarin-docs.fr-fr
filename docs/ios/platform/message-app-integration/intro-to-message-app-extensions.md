---
title: Notions de base sur l’extension d’application de message dans Xamarin. iOS
description: Cet article explique comment inclure une extension d’application de message dans une solution Xamarin. iOS qui s’intègre à l’application messages et présente de nouvelles fonctionnalités à l’utilisateur.
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/02/2017
ms.openlocfilehash: 37f2942c97f7604fbd72a6dd38de518d3668ee9e
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250135"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Notions de base sur l’extension d’application de message dans Xamarin. iOS

_Cet article explique comment inclure une extension d’application de message dans une solution Xamarin. iOS qui s’intègre à l’application messages et présente de nouvelles fonctionnalités à l’utilisateur._

Nouveauté d’iOS 10, une extension d’application de message s’intègre à l’application **messages** et présente de nouvelles fonctionnalités à l’utilisateur. L’extension peut envoyer du texte, des autocollants, des fichiers multimédias et des messages interactifs.

## <a name="about-message-app-extensions"></a>À propos des extensions d’application de message

Comme indiqué ci-dessus, une extension d’application de message s’intègre à l’application **messages** et présente de nouvelles fonctionnalités à l’utilisateur. L’extension peut envoyer du texte, des autocollants, des fichiers multimédias et des messages interactifs. Deux types d’extension d’application de message sont disponibles :

- **Vignettes-packs** : contient une collection d’autocollants que l’utilisateur peut ajouter à un message. Les packs d’autocollants peuvent être créés sans écrire de code.
- **application IMessage** : peut présenter une interface utilisateur personnalisée dans l’application messages pour sélectionner des autocollants, entrer du texte, y compris des fichiers multimédias (avec conversions de type facultatives) et créer, modifier et envoyer des messages d’interaction.

Les extensions de message Apps fournissent trois types de contenu principaux :

- **Messages interactifs** : type de contenu de message personnalisé généré par une application, lorsque l’utilisateur appuie sur le message, l’application est lancée au premier plan.
- **Autocollants** : images générées par l’application qui peuvent être incluses dans les messages envoyés entre les utilisateurs.
- **Autres contenus pris en charge** : l’application peut fournir du contenu tel que des photos, des vidéos, du texte ou des liens vers tout autre type de contenu qui a toujours été pris en charge par l’application messages.

Nouveauté d’iOS 10, l’application message intègre désormais son propre App Store dédié et intégré. Toutes les applications qui incluent des extensions de message apps sont affichées et promues dans ce magasin. Le tiroir de l’application nouveaux messages affiche toutes les applications qui ont été téléchargées à partir de l’App Store pour fournir un accès rapide aux utilisateurs.

Également nouveau dans iOS 10, Apple a ajouté l’attribution d’application inline qui permet à l’utilisateur de découvrir facilement une application. Par exemple, si un utilisateur envoie du contenu à un autre à partir d’une application que le 2e utilisateur n’a pas installée (comme un autocollant par exemple), le nom de l’application émettrice est listé sous le contenu de l’historique des messages. Si l’utilisateur appuie sur le nom de l’application, le message Store de l’application s’ouvre et l’application est sélectionnée dans le Store.

Les extensions de message apps sont similaires aux applications iOS existantes que le développeur est familiarisé avec la création et qu’elles ont accès à tous les frameworks et fonctionnalités standard d’une application iOS standard. Par exemple :

- Ils ont accès à l’achat dans l’application.
- Ils ont accès à Apple Pay.
- Ils ont accès au matériel de l’appareil, tel que l’appareil photo.

Les extensions de message apps sont uniquement prises en charge sur iOS 10. Toutefois, le contenu envoyé par ces extensions est affichable sur les appareils Watchos et macOS. La nouvelle _page récents_ ajoutée à Watchos 3 affiche les autocollants récents qui ont été envoyés à partir du téléphone, y compris ceux des extensions de message Apps, et autorise l’utilisateur à envoyer ces autocollants à partir de la montre.

## <a name="about-the-messages-framework"></a>À propos de l’infrastructure de messages

Nouveauté d’iOS 10, l’infrastructure de messages fournit l’interface entre l’extension de message Apps et l’application de message sur l’appareil iOS de l’utilisateur. Lorsque l’utilisateur lance une application à partir de l’application messages, cette infrastructure autorise la découverte de l’application et fournit les données et le contexte requis pour disposer son interface utilisateur.

Une fois l’application lancée, l’utilisateur interagit avec elle pour créer un nouveau contenu à partager via un message. L’application utilise ensuite l’infrastructure de messages pour transférer le contenu nouvellement créé vers l’application messages pour traitement.

Les extensions de l’infrastructure des messages et des applications de message sont basées sur les technologies existantes des extensions des applications iOS. Pour plus d’informations sur les extensions d’application, consultez le Guide de programmation de l' [extension d’application](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)Apple.

Contrairement à d’autres points d’extension fournis par Apple dans le système, le développeur n’a pas besoin de fournir une application hôte pour leurs extensions de message Apps puisque l’application de message lui-même fait office de conteneur. Toutefois, le développeur a la possibilité d’inclure l’extension de message apps à l’intérieur d’une application iOS nouvelle ou existante et de l’expédier avec le bundle.

Si les extensions message apps sont incluses dans l’offre groupée d’une application iOS, l’icône de l’application s’affiche à la fois sur l’écran d’accueil de l’appareil et dans le tiroir d’application de message à l’intérieur de l’application messages. S’il n’est pas inclus dans un bundle d’applications, l’extension de message Apps s’affiche uniquement dans le tiroir de message de l’application.

Même si les extensions de message apps ne sont pas incluses dans un bundle d’applications hôtes, le développeur doit fournir une icône d’application dans le bundle de l’extension de message Apps, car il s’agit de l’icône qui s’affiche dans d’autres parties du système, telles que le message d’application ou les paramètres , pour l’extension.

## <a name="about-stickers"></a>À propos des autocollants

Les autocollants Apple sont conçus de façon à permettre aux utilisateurs de la communication en autorisant l’envoi des autocollants inline comme tout autre contenu de message ou en les joignant à des bulles de messages précédentes au cours de la conversation.

Que sont les autocollants ?

- Il s’agit d’images fournies par l’extension de message apps.
- Il peut s’agir d’images animées ou statiques.
- Ils offrent un nouveau moyen de partager du contenu d’image depuis l’intérieur d’une application.

Il existe deux façons de créer des autocollants :

1. Vous pouvez créer des extensions d’applications de message de Pack d’autocollants à l’intérieur de Xcode sans inclure de code. Tout ce qui est nécessaire est les ressources pour les autocollants et les icônes d’application.
2. En créant une extension de message Apps standard qui fournit des autocollants à partir du code via l’infrastructure des messages.

### <a name="creating-sticker-packs"></a>Création de packs d’autocollants

Les packs d’autocollants sont créés à partir d’un modèle spécial à l’intérieur de Xcode et fournissent simplement un ensemble statique de ressources d’image qui peuvent être utilisées comme autocollants. Comme indiqué ci-dessus, ils ne nécessitent pas de code, le développeur fait simplement glisser des fichiers image dans le dossier de Pack d’autocollants à l’intérieur du catalogue de ressources autocollant.

Pour qu’une image soit incluse dans un pack d’autocollants, elle doit remplir les conditions suivantes :

- Les images doivent être au format PNG, APNG, GIF ou JPEG. Apple suggère d’utiliser uniquement les formats PNG et APNG lorsque vous fournissez des éléments autocollants.
- Les autocollants animés prennent uniquement en charge les formats APNG et GIF.
- Les images autocollantes doivent fournir un arrière-plan transparent, car elles peuvent être placées sur les bulles de message de la conversation par l’utilisateur.
- Les fichiers image individuels doivent être inférieurs à 500 Ko.
- Les images ne peuvent pas être inférieures à 100x100 points, ou supérieures à 206 x 206 points.

> [!IMPORTANT]
> Les images autocollantes doivent toujours être fournies `@3x` à la résolution de la plage de pixels 300 x 300 à 618 x 618. Le système génère automatiquement les `@2x` versions et `@1x` au moment de l’exécution en fonction des besoins.

Apple suggère de tester les éléments de l’image de la vignette par rapport à différents arrière-plans de couleur (tels que le blanc, le noir, le rouge, le jaune et le multicolore) et sur des photos, afin de s’assurer qu’ils recherchent le meilleur dans toutes les situations possibles.

Les autocollants peuvent fournir des autocollants dans l’une des trois tailles disponibles :

- **Petit** -100 x 100 points.
- **Moyenne** -136 x 136 points. Il s’agit de la taille par défaut.
- **Grand** -206 x 206 points.

Utilisez l’inspecteur d’attributs de Xcode pour définir la taille de la totalité du pack d’autocollants et fournir uniquement des ressources d’image qui correspondent à la taille demandée, pour obtenir les meilleurs résultats dans le navigateur des vignettes à l’intérieur de l’application messages.

Pour plus d’informations, consultez notre application [Ice CREME Builder](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder) et la [référence des messages](https://developer.apple.com/reference/messages)d’Apple.

## <a name="creating-a-custom-sticker-experience"></a>Création d’une expérience d’autocollant personnalisée

Si l’application nécessite davantage de contrôle ou de flexibilité que celle fournie par un pack d’autocollants, elle peut inclure une extension d’application de message et fournir les autocollants via l’infrastructure de messages pour une expérience d’autocollant personnalisée.

Quels sont les avantages de la création d’une expérience d’autocollant personnalisée ?

1. Permet à l’application de personnaliser le mode d’affichage des autocollants pour les utilisateurs de l’application. Par exemple, pour présenter des autocollants dans un format autre que la disposition grille standard ou sur un arrière-plan de couleur différent.
2. Permet de créer dynamiquement des autocollants à partir du code au lieu d’être inclus en tant que ressources d’images statiques.
3. Autorise le téléchargement dynamique des éléments d’image autocollants à partir du serveur Web du développeur sans avoir à publier une nouvelle version dans l’App Store.
4. Permet d’accéder à l’appareil photo de l’appareil pour créer des autocollants à la volée.
5. Permet des achats dans l’application afin que l’utilisateur puisse acheter plus d’autocollants à l’intérieur de l’application.

Pour créer une expérience d’autocollant personnalisée, procédez comme suit :

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Démarrez Visual Studio pour Mac.
2. Ouvrez la solution pour ajouter une extension d’application de message à.
3. Sélectionnez**extension IMessage** des**Extensions** >  **iOS** > et cliquez sur le bouton **suivant** :

    [![](intro-to-message-app-extensions-images/message01.png "Sélectionner l’extension iMessage")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Entrez un **nom d’extension** , puis cliquez sur le bouton **suivant** :

    [![](intro-to-message-app-extensions-images/message02.png "Entrer un nom d’extension")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. Cliquez sur le bouton **créer** pour générer l’extension :

    [![](intro-to-message-app-extensions-images/message03.png "Cliquez sur le bouton créer")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Démarrez Visual Studio.
2. Ouvrez la solution pour ajouter une extension d’application de message.
3. Sélectionnez **Extensions ios > extension IMessage (IOS)** , puis cliquez sur le bouton **suivant** :

    [![Sélectionner l’extension iMessage (iOS)](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. Entrez un **nom** et cliquez sur le bouton **OK**

-----

Par défaut, le `MessagesViewController.cs` fichier est ajouté à la solution. Il s’agit du point d’entrée principal dans l’extension et il hérite `MSMessageAppViewController` de la classe.

L’infrastructure de messages fournit des classes pour la présentation des autocollants disponibles à l’utilisateur :

- `MSStickerBrowserViewController`-Contrôle la vue dans laquelle les autocollants seront présentés. Il est également conforme à l' `IMSStickerBrowserViewDataSource` interface pour retourner le nombre d’autocollants et l’autocollant pour un index de navigateur donné.
- `MSStickerBrowserView`-Il s’agit de la vue dans laquelle les autocollants disponibles seront affichés.
- `MSStickerSize`-Détermine les tailles des cellules individuelles pour la grille des autocollants présentée dans la vue du navigateur.

### <a name="creating-a-custom-sticker-browser"></a>Création d’un navigateur de vignettes personnalisé

Le développeur peut personnaliser davantage l’utilisation des autocollants pour l’utilisateur en fournissant un navigateur de`MSMessageAppBrowserViewController`vignettes personnalisé () dans l’extension de l’application de message. Le navigateur de vignettes personnalisées change la façon dont les autocollants sont présentés à l’utilisateur lorsqu’ils sélectionnent un autocollant à inclure dans le flux de message.

Effectuez ce qui suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans le **panneau solutions**, cliquez avec le bouton droit sur le nom du projet de l’extension et sélectionnez **Ajouter** > **un nouveau fichier...**  >  **iOS |**  > **Contrôleur d’interface**Apple Watch.
2. Entrez `StickerBrowserViewController` pour le **nom** et cliquez sur le bouton **nouveau** :

    [![](intro-to-message-app-extensions-images/browser01.png "Entrez StickerBrowserViewController pour le nom")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Ouvrez le `StickerBrowserViewController.cs` fichier pour le modifier.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet de l’extension et sélectionnez **Ajouter** > **un nouveau fichier...**  >  **iOS |**  > **Contrôleur d’interface**Apple Watch.
2. Entrez `StickerBrowserViewController` pour le **nom** et cliquez sur le bouton **nouveau** :

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "Entrez StickerBrowserViewController pour le nom")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. Ouvrez le `StickerBrowserViewController.cs` fichier pour le modifier.

-----

`StickerBrowserViewController.cs` Procédez comme suit :

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MonkeyStickers
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MSStickerSize stickerSize) : base (stickerSize)
        {
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            ...
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();
        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            return Stickers[(int)index];
        }
        #endregion
    }
}
```

Examinez le code ci-dessus en détail. Il crée un stockage pour les autocollants fournis par l’extension :

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

Et substitue deux méthodes de la `MSStickerBrowserViewController` classe pour fournir des données pour le navigateur à partir de ce magasin de données :

```csharp
public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
{
    return Stickers.Count;
}

public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
{
    return Stickers[(int)index];
}
```

La `CreateSticker` méthode obtient le chemin d’accès d’une ressource d’image à partir de l’offre groupée de l’extension et l' `MSSticker` utilise pour créer une nouvelle instance d’un à partir de cette ressource, qu’elle ajoute à la collection :

```csharp
private void CreateSticker (string assetName, string localizedDescription)
{

    // Get path to asset
    var path = NSBundle.MainBundle.PathForResource (assetName, "png");
    if (path == null) {
        Console.WriteLine ("Couldn't create sticker {0}.", assetName);
        return;
    }

    // Build new sticker
    var stickerURL = new NSUrl (path);
    NSError error = null;
    var sticker = new MSSticker (stickerURL, localizedDescription, out error);
    if (error == null) {
        // Add to collection
        Stickers.Add (sticker);
    } else {
        // Report error
        Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
    }
}
```

La `LoadSticker` méthode est appelée à `ViewDidLoad` partir de pour créer un autocollant à partir de la ressource d’image nommée (incluse dans l’offre groupée de l’application) et l’ajouter à la collection d’autocollants.

Pour implémenter le navigateur de vignettes personnalisé `MessagesViewController.cs` , modifiez le fichier et faites-le ressembler à ce qui suit :

```csharp
using System;
using UIKit;
using Messages;

namespace MonkeyStickers
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public StickerBrowserViewController BrowserViewController { get; set;}
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);
        }
        #endregion
    }
}
```

En examinant ce code en détail, il crée un stockage pour le navigateur personnalisé :

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

Et dans la `ViewDidLoad` méthode, il instancie et configure un nouveau navigateur :

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

Ensuite, il ajoute le navigateur à la vue pour l’afficher :

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Personnalisation des autocollants

Il est possible de personnaliser davantage les vignettes en incluant uniquement deux classes dans l’extension de l’application de message :

- `MSStickerView`
- `MSSticker`

À l’aide des méthodes ci-dessus, l’extension peut prendre en charge la sélection de vignettes qui ne repose pas sur la méthode de navigateur de vignettes standard. En outre, l’affichage des autocollants peut être basculé entre deux modes d’affichage différents :

- **Compact** : il s’agit du mode par défaut dans lequel la vue autocollant occupe les 25% inférieurs de la vue des messages.
- **Développé** : la vue d’autocollant remplit l’intégralité de l’affichage des messages.

Cette vue d’autocollant peut être basculée entre ces modes par l’utilisateur, par programmation ou manuellement.

Jetez un coup d’œil à l’exemple suivant pour gérer le basculement entre les deux modes d’affichage. Deux contrôleurs d’affichage différents seront nécessaires pour chaque État. Le `StickerBrowserViewController` gère l’affichage **compact** et ressemble à ce qui suit :

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MessageExtension
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set; }
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MessagesViewController messagesAppViewController, MSStickerSize stickerSize) : base (stickerSize)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("add", "Add New Sticker");
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();

        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            // Wanting to add a new sticker?
            if (index == 0) {
                // Yes, ask controller to present add sticker interface
                MessagesAppViewController.AddNewSticker ();
                return null;
            } else {
                // No, return existing sticker
                return Stickers [(int)index];
            }
        }
        #endregion
    }
}
```

Le `AddStickerViewController` gère l’affichage autocollant **développé** et se présente comme suit :

```csharp
using System;
using Foundation;
using UIKit;
using Messages;

namespace MessageExtension
{
    public class AddStickerViewController : UIViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set;}
        public MSSticker NewSticker { get; set;}
        #endregion

        #region Constructors
        public AddStickerViewController (MessagesViewController messagesAppViewController)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Override Method
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Build interface to create new sticker
            var cancelButton = new UIButton (UIButtonType.RoundedRect);
            cancelButton.TouchDown += (sender, e) => {
                // Cancel add new sticker
                MessagesAppViewController.CancelAddNewSticker ();
            };
            View.AddSubview (cancelButton);

            var doneButton = new UIButton (UIButtonType.RoundedRect);
            doneButton.TouchDown += (sender, e) => {
                // Add new sticker to collection
                MessagesAppViewController.AddStickerToCollection (NewSticker);
            };
            View.AddSubview (doneButton);

            ...
        }
        #endregion
    }
}
```

Le `MessageViewController` implémente ces contrôleurs d’affichage pour déterminer l’État demandé :

```csharp
using System;
using UIKit;
using Messages;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public bool IsAddingSticker { get; set;}
        public StickerBrowserViewController BrowserViewController { get; set; }
        public AddStickerViewController AddStickerController { get; set;}
        #endregion

        #region Constructors
        protected MessagesViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Public Methods
        public void PresentStickerBrowser ()
        {
            // Is the Add sticker view being displayed?
            if (IsAddingSticker) {
                // Yes, remove it from view
                AddStickerController.RemoveFromParentViewController ();
                AddStickerController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);

            // Save mode
            IsAddingSticker = false;
        }

        public void PresentAddSticker ()
        {
            // Is the sticker browser being displayed?
            if (!IsAddingSticker) {
                // Yes, remove it from view
                BrowserViewController.RemoveFromParentViewController ();
                BrowserViewController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (AddStickerController);
            AddStickerController.DidMoveToParentViewController (this);
            View.AddSubview (AddStickerController.View);

            // Save mode
            IsAddingSticker = true;
        }

        public void AddNewSticker ()
        {
            // Switch to expanded view mode
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void CancelAddNewSticker ()
        {
            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }

        public void AddStickerToCollection (MSSticker sticker)
        {
            // Add sticker to collection
            BrowserViewController.Stickers.Add (sticker);

            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (this, MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Create new Add controller and configure it as well
            AddStickerController = new AddStickerViewController (this);
            AddStickerController.View.Frame = View.Frame;

            // Initially present the sticker browser
            PresentStickerBrowser ();
        }

        public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            base.DidTransition (presentationStyle);

            // Take action based on style
            switch (presentationStyle) {
            case MSMessagesAppPresentationStyle.Compact:
                PresentStickerBrowser ();
                break;
            case MSMessagesAppPresentationStyle.Expanded:
                PresentAddSticker ();
                break;
            }
        }
        #endregion
    }
}
```

Quand l’utilisateur demande à ajouter un nouvel autocollant à son regroupement disponible, un nouveau `AddStickerViewController` est rendu visible par le contrôleur et la vue autocollante s’affiche dans la vue **développée** :

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

Lorsque l’utilisateur choisit un autocollant à ajouter, il est ajouté à sa collection disponible et la vue **compacte** est demandée :

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
}
```

La `DidTransition` méthode est substituée pour gérer le basculement entre les deux modes :

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
```

## <a name="summary"></a>Récapitulatif

Cet article a été abordé dans le cas d’une extension d’application de message dans une solution Xamarin. iOS qui s’intègre à l’application **messages** et présente de nouvelles fonctionnalités à l’utilisateur. Elle est traitée à l’aide de l’extension pour envoyer du texte, des autocollants, des fichiers multimédias et des messages interactifs.

## <a name="related-links"></a>Liens associés

- [Générateur de crème glacée (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-icecreambuilder)
- [Référence des messages](https://developer.apple.com/reference/messages)
- [Guide de programmation de l’extension d’application](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
