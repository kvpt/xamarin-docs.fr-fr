---
title: Principes fondamentaux des messages Extension d’application dans Xamarin.iOS
description: Cet article montre comment inclure une Extension d’application de Message dans une solution Xamarin.iOS qui s’intègre à l’application Messages et présente les nouvelles fonctionnalités à l’utilisateur.
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7bd4a87843852e940da96f688371ddbecbf7e0b4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105777"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Principes fondamentaux des messages Extension d’application dans Xamarin.iOS

_Cet article montre comment inclure une Extension d’application de Message dans une solution Xamarin.iOS qui s’intègre à l’application Messages et présente les nouvelles fonctionnalités à l’utilisateur._

Nouveau à iOS 10, une Extension d’application Message intègre le **Messages** application et présente de nouvelles fonctionnalités à l’utilisateur. L’extension peut envoyer des messages interactifs, texte, autocollants et fichiers multimédias.

## <a name="about-message-app-extensions"></a>À propos des Extensions d’application Message

Comme indiqué ci-dessus, une Extension d’application Message intègre le **Messages** application et présente de nouvelles fonctionnalités à l’utilisateur. L’extension peut envoyer des messages interactifs, texte, autocollants et fichiers multimédias. Deux types d’Extension d’application de Message sont disponibles :

- **Packs d’autocollant** -contient une collection d’autocollants de l’utilisateur peut ajouter à un message. Packs d’autocollant peuvent être créés sans écrire de code.
- **iMessage application** -peut présenter une Interface utilisateur personnalisée dans l’application Messages pour la sélection autocollants, saisie de texte, y compris les fichiers de support (avec des conversions de type facultatif) et création, modification et envoi de messages de l’interaction.

Extensions des applications Message fournissent trois principaux types de contenu :

- **Des Messages interactifs** -sont un type de contenu de message personnalisé qui génère une application, lorsque l’utilisateur appuie sur le message, l’application est lancée au premier plan.
- **Autocollants** -sont des images générées par l’application qui peut être incluse dans les messages envoyés entre les utilisateurs.
- **Autre contenu pris en charge** : l’application peut fournir le contenu tel que des photos, vidéos, du texte ou des liens vers tout autre contenu des types qui ont toujours été pris en charge par l’application de Messages.

Nouveau à iOS 10, l’application Message inclut désormais son propre Store application dédié, intégrés. Toutes les applications qui incluent des Extensions d’applications de Message seront affiche et promues dans ce magasin. Le tiroir application de nouveaux Messages affiche toutes les applications qui ont été téléchargées à partir du Store application Messages pour fournir un accès rapide aux utilisateurs.

Également nouveau dans iOS 10, Apple a ajouté les Attribution application Inline qui permet à l’utilisateur à découvrir facilement une application. Par exemple, si un utilisateur envoie le contenu vers un autre à partir d’une application qui n’a pas l’utilisateur 2nd installé (par exemple, un autocollant par exemple), le nom de l’application émettrice est répertorié sous le contenu de l’historique des messages. Si l’utilisateur appuie sur l’application nom, le Store application Message nous est ouvert et l’application sélectionnée dans le magasin.

Extensions des applications de message sont similaires aux applications iOS existantes que le développeur est familiarisé avec la création et ils ont accès à toutes les infrastructures standards et les fonctionnalités d’une application iOS standard. Exemple :

- Ils ont accès à l’achat dans l’application.
- Ils ont accès à Apple Pay.
- Ils ont accès au matériel tels que l’appareil photo de l’appareil.

Extensions des applications de message sont uniquement pris en charge sur iOS 10, toutefois, le contenu qui envoient ces Extensions est visible sur les appareils watchOS et macOS. La nouvelle _récents Page_ ajouté à watchOS 3, affiche les autocollants récents qui ont été envoyés à partir du téléphone, y compris ceux à partir des Extensions d’applications de Message, et l’utilisateur d’envoyer ces autocollants de l’Apple watch.

## <a name="about-the-messages-framework"></a>À propos de l’infrastructure de Messages

Nouveau dans iOS 10, l’infrastructure de Messages fournit l’interface entre l’Extension d’applications de Message et de l’application Message appareil iOS de l’utilisateur. Lorsque l’utilisateur lance une application à partir à l’intérieur de l’application Messages, cette infrastructure permet à l’application d’être découvert et fournit les données et le contexte nécessaire pour disposer de son interface utilisateur.

Une fois que l’application est lancée, l’utilisateur interagit avec lui pour créer du contenu à partager via un message. L’application utilise ensuite l’infrastructure de Messages à transférer le contenu nouvellement créé à l’application Messages pour le traitement.

Le framework de Messages et des Extensions d’applications de Message sont appuient sur iOS préexistant technologies des Extensions d’application. Pour plus d’informations sur les Extensions d’application, consultez le site d’Apple [Guide de programmation Extension application](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

Contrairement à d’autres Points d’Extension Apple a fourni dans le système, le développeur ne doive pas fournir une application hôte pour les Extensions de leurs applications de Message dans la mesure où l’application de Message lui-même agit comme conteneur. Toutefois, le développeur a la possibilité d’y compris l’Extension d’applications de Message à l’intérieur d’une application iOS de nouveau ou existant et sa livraison, ainsi que l’offre groupée.

Si les Extensions d’applications de Message est incluses dans l’offre groupée d’une application iOS, icône de l’application sera affiché à la fois sur l’écran d’accueil de l’appareil et dans le tiroir des applications à partir de Message dans l’application Messages. S’il n’est pas inclus dans un lot d’applications, l’Extension d’applications de Message uniquement être affichera dans le tiroir des applications Message.

Même si les Extensions d’applications de Message n’est pas incluses dans un lot d’applications hôtes, le développeur devra fournir une icône d’application dans l’offre groupée de l’Extension d’applications Message, car il s’agit d’icône qui s’affichera dans d’autres parties du système telles que le tiroir d’application de Message ou les paramètres , pour l’Extension.

## <a name="about-stickers"></a>Sur les autocollants

Apple conçue autocollants comme nouveau moyen permettant aux utilisateurs d’iMessage de communiquer en autorisant des autocollants à envoyer inline comme tout autre contenu de message, ou ils peuvent être attachés aux bulles de message précédent à l’intérieur de la conversation.

Quelles sont les autocollants ?

- Ils sont des images fournies par l’Extension d’applications de Message.
- Ils peuvent être des images animés ou statiques.
- Ils fournissent une nouvelle façon de partager du contenu à partir de l’image à l’intérieur d’une application.

Il existe deux façons de créer des autocollants :

1. Un autocollant Pack Message applications Extensions peuvent être créées à partir d’à l’intérieur de Xcode sans inclure n’importe quel code. Tout ce qui est nécessaire est les ressources pour les autocollants et les icônes d’application.
2. En créant une Extension d’applications de Message standard qui fournit des autocollants à partir du code par le biais de l’infrastructure de Messages.

### <a name="creating-sticker-packs"></a>Création de packs d’autocollant

Packs d’autocollant sont créés à partir d’un modèle spécial à l’intérieur de Xcode et fournissent simplement un ensemble statique de ressources d’image qui peut être utilisé comme autocollants. Comme indiqué ci-dessus, ils ne nécessitent pas de code, le développeur fait simplement glisser les fichiers image dans le dossier de Pack d’autocollant à l’intérieur du catalogue autocollants.

Pour une image à inclure dans un Pack d’autocollant, il doit remplir les conditions suivantes :

- Images doivent être au format PNG, APNG, GIF ou JPEG. Apple suggère à l’aide uniquement les formats PNG et APNG lors de la fourniture de ressources de l’autocollant.
- Autocollants animées prennent uniquement en charge les formats APNG et GIF.
- Images de vignette doivent fournir un arrière-plan transparent, car ils peuvent être placés sur les bulles de message dans la conversation par l’utilisateur.
- Les fichiers image individuelle doivent être inférieure à 500 Ko.
- Les images ne peut pas être inférieure à 100 x 100 points ou plus qui pointe 206 x 206.

> [!IMPORTANT]
> Images de vignette doivent toujours être fournis à la `@3x` résolution dans la plage de 300 x 300 à 618 x 618 pixel. Le système génère automatiquement le `@2x` et `@1x` versions lors de l’exécution en fonction des besoins.

Apple suggère les ressources d’Image autocollant sur différents arrière-plans différents (par exemple, blanc, noir, rouge, jaune et multicolores) et les photos de plus, pour vous assurer qu’ils présentent le meilleur dans toutes les situations possibles de test.

Packs d’autocollant peuvent fournir des autocollants dans un des trois tailles disponibles :

- **Petite** - 100 x 100 points.
- **Support** - 136 x 136 points. Il s’agit de la taille par défaut.
- **Grandes** - 206 x 206 points.

Utilisez l’inspecteur d’attributs de Xcode pour définir la taille pour tout le Pack d’autocollant et fournissez uniquement les ressources d’image qui correspond à la taille demandée, pour de meilleurs résultats dans le navigateur d’autocollant à l’intérieur de l’application Messages.

Pour plus d’informations, consultez notre [Ice cream Générateur](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) application et Apple [Messages référence](https://developer.apple.com/reference/messages).

## <a name="creating-a-custom-sticker-experience"></a>Création d’une expérience personnalisée autocollant

Si l’application nécessite plus de contrôle ou de flexibilité que celle fournie par un Pack d’autocollant, il peut inclure une Extension d’application Message et fournir les autocollants via l’infrastructure de Messages pour une expérience d’autocollant personnalisé.

Quels sont les avantages de la création d’une expérience d’autocollant personnalisé ?

1. Autorise l’application à personnaliser l’affichent des autocollants aux utilisateurs de l’application. Par exemple, pour les autocollants présents dans un format autre que la mise en page de grille standard ou sur un arrière-plan de couleur différent.
2. Permet des autocollants à être créées dynamiquement à partir du code au lieu d’être inclus en tant que ressources d’image statique.
3. Permet à des ressources d’image autocollant à être téléchargée dynamiquement à partir du serveur web pour du développeur sans avoir à lancer une nouvelle version à l’App Store.
4. Permet pour l’accès de photo l’appareil pour créer des autocollants à la volée.
5. Permet d’achats dans l’application afin de l’utilisateur peut acheter plusieurs autocollants d’à l’intérieur de l’application.

À la création d’une expérience d’autocollant personnalisé, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Démarrez Visual Studio pour Mac.
2. Ouvrez la solution pour ajouter une Extension d’application de Message. 
3. Sélectionnez **iOS** > **Extensions** > **Extension iMessage** et cliquez sur le **suivant** bouton : 

    [![](intro-to-message-app-extensions-images/message01.png "Sélectionnez l’Extension iMessage")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Entrez un **nom de l’Extension** et cliquez sur le **suivant** bouton : 

    [![](intro-to-message-app-extensions-images/message02.png "Entrez un nom d’Extension")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. Cliquez sur le **créer** pour générer l’Extension : 

    [![](intro-to-message-app-extensions-images/message03.png "Cliquez sur le bouton Créer")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Démarrez Visual Studio.
2. Ouvrez la solution pour ajouter une Extension d’application de Message.
3. Sélectionnez ** Extensions iOS > Extension iMessage (iOS) ** et cliquez sur le **suivant** bouton :

    [![Sélectionnez l’Extension iMessage (iOS)](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. Entrez un **nom** et cliquez sur le **OK** bouton

-----

Par défaut, le `MessagesViewController.cs` fichier sera ajouté à la solution. Il s’agit du point d’entrée principal à l’Extension et elle hérite la `MSMessageAppViewController` classe.

L’infrastructure de Messages fournit des classes pour la présentation des autocollants disponibles à l’utilisateur :

- `MSStickerBrowserViewController` : Contrôle les autocollants seront afficheront dans la vue. Il est conforme également à la `IMSStickerBrowserViewDataSource` interface à retourner le nombre de vignette et de la vignette pour un index donné de navigateur.
- `MSStickerBrowserView` -Il s’agit de la vue qui s’affichera dans les autocollants disponibles.
- `MSStickerSize` -Décide les tailles de cellule pour la grille des autocollants présentées dans la vue du navigateur.

### <a name="creating-a-custom-sticker-browser"></a>Création d’un navigateur personnalisé autocollant

Le développeur peut personnaliser davantage l’expérience de la vignette pour l’utilisateur en fournissant un navigateur d’autocollant personnalisé (`MSMessageAppBrowserViewController`) dans l’Extension d’application de Message. Le navigateur d’autocollant personnalisé modifie la façon dont les autocollants sont présentés à l’utilisateur lorsqu’ils sont en sélectionnant un autocollant à inclure dans le flux de message.

Effectuez ce qui suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans le **panneau solutions**, avec le bouton droit sur le nom de l’Extension du projet et sélectionnez **ajouter** > **nouveau fichier...**   >  **iOS | Apple Watch** > **contrôleur d’Interface**.
2. Entrez `StickerBrowserViewController` pour le **nom** et cliquez sur le **New** bouton : 

    [![](intro-to-message-app-extensions-images/browser01.png "Entrez StickerBrowserViewController pour le nom")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Ouvrez le `StickerBrowserViewController.cs` fichier pour le modifier.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans le **l’Explorateur de solutions**, avec le bouton droit sur le nom de l’Extension du projet et sélectionnez **ajouter** > **nouveau fichier...**   >  **iOS | Apple Watch** > **contrôleur d’Interface**.
2. Entrez `StickerBrowserViewController` pour le **nom** et cliquez sur le **New** bouton : 

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "Entrez StickerBrowserViewController pour le nom")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. Ouvrez le `StickerBrowserViewController.cs` fichier pour le modifier.

-----

Rendre le `StickerBrowserViewController.cs` ressembler au suivant :

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

Examinons le code ci-dessus en détail. Il crée le stockage pour les autocollants qui fournit l’Extension :

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

Et remplace deux méthodes de la `MSStickerBrowserViewController` classe pour fournir des données pour le navigateur à partir de ce magasin de données :

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

Le `CreateSticker` méthode obtient le chemin d’accès d’une ressource image à partir de l’offre groupée de l’Extension et l’utilise pour créer une nouvelle instance d’un `MSSticker` à partir de cette ressource, il ajoute à la collection :

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

Le `LoadSticker` méthode est appelée à partir de `ViewDidLoad` pour créer une vignette à partir de la ressource d’image nommée (incluse dans le bundle de l’application) et l’ajouter à la collection des autocollants.

Pour implémenter le navigateur d’autocollant personnalisé, vous devez modifier le `MessagesViewController.cs` de fichier et qu’elle ressemble à ce qui suit :

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

Jeter un coup de œil sur ce code en détail, il crée le stockage pour le navigateur personnalisé :

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

Puis, dans le `ViewDidLoad` (méthode), il instancie et configure une nouvelle fenêtre de navigateur :

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

Il ajoute ensuite le navigateur à la vue pour l’afficher :

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Personnalisation plus poussée autocollant

Une personnalisation plus poussée autocollant est possible en incluant simplement deux classes dans l’Extension d’application de Message :

- `MSStickerView`
- `MSSticker`

À l’aide des méthodes ci-dessus, l’Extension peut prendre en charge sélection autocollant qui ne repose pas sur la méthode du navigateur de l’autocollant standard. En outre, l’affichage de l’autocollant possible de faire basculer entre les deux modes d’affichage différents :

- **Compact** -il s’agit du mode par défaut où la vue de la vignette occupe le bas 25 % de la vue de Message.
- **Développé** -la vue de la vignette remplit toute la vue de Message.

Cette vue de l’autocollant est possible entre ces modes par programme ou manuellement par l’utilisateur.

Examinons l’exemple suivant de gérer le basculement entre les deux modes d’affichage différent. Deux contrôleurs d’affichage différentes seront nécessaires pour chaque état. Le `StickerBrowserViewController` gère la **Compact** vue et se présente comme suit :

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

Le `AddStickerViewController` gérera le **développé** autocollant vue et ressemble à ce qui suit :

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

Le `MessageViewController` implémente ces contrôleurs d’affichage pour définir l’état demandé :

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

Lorsque l’utilisateur demande à ajouter une nouvelle vignette à leur collection disponible, un nouveau `AddStickerViewController` devient le contrôleur visible et l’affichage de l’autocollant saisit le **développé** vue :

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

Lorsque l’utilisateur choisit un autocollant à ajouter, il est ajouté à leur collection disponible et la **Compact** vue est demandée :

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
}
```

Le `DidTransition` méthode est substituée pour gérer le changement entre les deux modes :

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

Cet article a présenté inclure une Extension d’application de Message dans une solution Xamarin.iOS qui s’intègre à la **Messages** application et les fonctionnalités de nouveau présente à l’utilisateur. Il couvert à l’aide de l’extension pour envoyer des messages interactifs, texte, autocollants et fichiers multimédias.



## <a name="related-links"></a>Liens associés

- [Générateur de glace (exemple)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Référence des messages](https://developer.apple.com/reference/messages)
- [Guide de programmation d’Extension d’application](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
