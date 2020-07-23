---
title: Notifications utilisateur avancées dans Xamarin. iOS
description: Cet article examine en détail l’infrastructure de notifications utilisateur, introduite dans iOS 10. Il aborde les notifications utilisateur, l’interface de notification utilisateur, les pièces jointes de média, les interfaces utilisateur personnalisées, etc.
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: edf224428103c318307f3a05dc79e1e8d286f512
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86935112"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Notifications utilisateur avancées dans Xamarin. iOS

Nouveauté d’iOS 10, l’infrastructure de notification utilisateur permet la remise et la gestion des notifications locales et distantes. À l’aide de ce Framework, une application ou une extension d’application peut planifier la remise de notifications locales en spécifiant un ensemble de conditions, telles que l’emplacement ou l’heure de la journée.

## <a name="about-user-notifications"></a>À propos des notifications utilisateur

La nouvelle infrastructure de notification utilisateur autorise la remise et la gestion des notifications locales et distantes. À l’aide de ce Framework, une application ou une extension d’application peut planifier la remise de notifications locales en spécifiant un ensemble de conditions, telles que l’emplacement ou l’heure de la journée.

En outre, l’application ou l’extension peut recevoir (et éventuellement modifier) des notifications locales et distantes quand elles sont remises à l’appareil iOS de l’utilisateur.

La nouvelle infrastructure d’interface utilisateur de notification utilisateur permet à une application ou à une extension d’application de personnaliser l’apparence des notifications locales et distantes quand elles sont présentées à l’utilisateur.

Cette infrastructure offre les moyens suivants pour une application de remettre des notifications à un utilisateur :

- **Alertes visuelles** : la notification est remontée à partir du haut de l’écran sous la forme d’une bannière.
- **Sons et vibrations** -peuvent être associés à une notification.
- **Icône d’application badge** -où l’icône de l’application affiche un badge indiquant que le nouveau contenu est disponible. Tels que le nombre de messages électroniques non lus.

En outre, selon le contexte actuel de l’utilisateur, il existe différentes façons de présenter une notification :

- Si l’appareil est déverrouillé, la notification est reportée à partir du haut de l’écran sous la forme d’une bannière.
- Si l’appareil est verrouillé, la notification s’affiche sur l’écran de verrouillage de l’utilisateur.
- Si l’utilisateur a manqué une notification, il peut ouvrir le centre de notification et afficher toutes les notifications disponibles en attente.

Une application Xamarin. iOS a deux types de notifications utilisateur qu’elle peut envoyer :

- **Notifications locales** : celles-ci sont envoyées par les applications installées localement sur l’appareil de l’utilisateur.
- Les **notifications distantes** sont envoyées à partir d’un serveur distant et présentées à l’utilisateur ou déclenchent une mise à jour en arrière-plan du contenu de l’application.

Pour plus d’informations, consultez notre documentation sur les [notifications utilisateur améliorées](~/ios/platform/user-notifications/enhanced-user-notifications.md) .

## <a name="the-new-user-notification-interface"></a>Nouvelle interface de notification utilisateur

Les notifications utilisateur dans iOS 10 sont présentées avec une nouvelle conception d’interface utilisateur qui fournit davantage de contenu, comme un titre, un sous-titre et des pièces jointes facultatives, qui peuvent être présentées sur l’écran de verrouillage, sous la forme d’une bannière en haut de l’appareil ou dans le centre de notification.

Quel que soit l’emplacement d’affichage d’une notification utilisateur dans iOS 10, le même aspect et la même fonctionnalité sont présentés avec les mêmes fonctionnalités.

Dans iOS 8, Apple a introduit des notifications actionnables permettant au développeur de joindre des actions personnalisées à une notification et de permettre à l’utilisateur d’agir sur une notification sans avoir à lancer l’application. Dans iOS 9, Apple a amélioré les notifications exploitables avec la réponse rapide, ce qui permet à l’utilisateur de répondre à une notification avec entrée de texte.

Étant donné que les notifications utilisateur font partie intégrante de l’expérience utilisateur dans iOS 10, Apple a encore développé des notifications actionnables pour prendre en charge le toucher 3D, où l’utilisateur appuie sur une notification et une interface utilisateur personnalisée s’affiche pour fournir une interaction enrichie avec la notification.

Lorsque l’interface utilisateur de notification utilisateur personnalisée s’affiche, si l’utilisateur interagit avec des actions attachées à la notification, l’interface utilisateur personnalisée peut être instantanément mise à jour pour fournir des commentaires sur ce qui a été modifié.

Nouveauté d’iOS 10, l’API d’interface utilisateur de notification utilisateur permet à une application Xamarin. iOS de tirer facilement parti de ces nouvelles fonctionnalités d’interface utilisateur de notification.

## <a name="adding-media-attachments"></a>Ajout de pièces jointes de média

L’un des éléments les plus courants qui sont partagés entre les utilisateurs est photos. par conséquent, iOS 10 a ajouté la possibilité de joindre un élément multimédia (par exemple, une photo) directement à une notification, où il sera présenté et facilement accessible à l’utilisateur, ainsi que le reste du contenu de la notification.

Toutefois, en raison de la taille impliquée dans l’envoi d’une petite image, son attachement à une charge utile de notification distante devient irréalisable. Pour gérer cette situation, le développeur peut utiliser la nouvelle extension de service dans iOS 10 pour télécharger l’image à partir d’une autre source (par exemple, une banque de données CloudKit) et l’attacher au contenu de la notification avant qu’elle ne soit affichée à l’utilisateur.

Pour qu’une notification à distance soit modifiée par une extension de service, sa charge utile doit être marquée comme mutable. Par exemple :

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

Jetez un coup d’œil à la vue d’ensemble suivante du processus :

[![Ajout du processus de pièces jointes multimédias](advanced-user-notifications-images/extension02.png)](advanced-user-notifications-images/extension02.png#lightbox)

Une fois que la notification à distance est remise à l’appareil (via APNs), l’extension de service peut ensuite télécharger l’image requise par le biais de l’un des moyens souhaités (par exemple, `NSURLSession` ) et après avoir reçu l’image, elle peut modifier le contenu de la notification et l’afficher à l’utilisateur.

L’exemple suivant illustre la façon dont ce processus peut être géré dans le code :

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyNotification
{
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Constructors
        public NotificationService ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            // Get file URL
            var attachementPath = request.Content.UserInfo.ObjectForKey (new NSString ("my-attachment"));
            var url = new NSUrl (attachementPath.ToString ());

            // Download the file
            var localURL = new NSUrl ("PathToLocalCopy");

            // Create attachment
            var attachmentID = "image";
            var options = new UNNotificationAttachmentOptions ();
            NSError err;
            var attachment = UNNotificationAttachment.FromIdentifier (attachmentID, localURL, options , out err);

            // Modify contents
            var content = request.Content.MutableCopy() as UNMutableNotificationContent;
            content.Attachments = new UNNotificationAttachment [] { attachment };

            // Display notification
            contentHandler (content);
        }

        public override void TimeWillExpire ()
        {
            // Handle service timing out
        }
        #endregion
    }
}
```

Lorsque la notification est reçue à partir de APNs, l’adresse personnalisée de l’image est lue à partir du contenu et le fichier est téléchargé à partir du serveur. Un `UNNotificationAttachement` est ensuite créé avec un ID unique et l’emplacement local de l’image (en tant que `NSUrl` ). Une copie mutable du contenu de notification est créée et des pièces jointes de média sont ajoutées. Enfin, la notification est présentée à l’utilisateur en appelant `contentHandler` .

Une fois qu’une pièce jointe a été ajoutée à une notification, le système prend en charge le déplacement et la gestion du fichier.

En plus des notifications distantes présentées ci-dessus, les pièces jointes sont également prises en charge à partir des notifications locales, où la `UNNotificationAttachement` est créée et jointe à la notification avec son contenu.

La notification dans iOS 10 prend en charge les pièces jointes d’images (statiques et GIF), l’audio ou la vidéo et le système affiche automatiquement l’interface utilisateur personnalisée appropriée pour chacun de ces types de pièces jointes lorsque la notification est présentée à l’utilisateur.

> [!NOTE]
> Vous devez veiller à optimiser à la fois la taille du média et le temps nécessaire pour télécharger le média à partir du serveur distant (ou pour assembler le média pour les notifications locales), car le système impose des limites strictes aux deux lors de l’exécution de l’extension de service de l’application. Par exemple, envisagez d’envoyer une version réduite de l’image ou un petit clip d’une vidéo à présenter dans la notification.

## <a name="creating-custom-user-interfaces"></a>Création d’interfaces utilisateur personnalisées

Pour créer une interface utilisateur personnalisée pour ses notifications utilisateur, le développeur doit ajouter une extension de contenu de notification (nouveauté d’iOS 10) à la solution de l’application.

L’extension de contenu de notification permet au développeur d’ajouter ses propres affichages à l’interface utilisateur de notification et de dessiner le contenu souhaité. À compter d’iOS 12, les extensions de contenu de notification prennent en charge des contrôles d’interface utilisateur interactifs tels que les boutons et les curseurs. Pour plus d’informations, consultez la documentation [interactive notifications dans IOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) .

Pour prendre en charge l’interaction de l’utilisateur avec une notification utilisateur, des actions personnalisées doivent être créées, inscrites auprès du système et jointes à la notification avant d’être planifiée sur le système. L’extension de contenu de notification sera appelée pour gérer le traitement de ces actions. Pour plus d’informations sur les actions personnalisées, consultez la section [utilisation des actions de notification](~/ios/platform/user-notifications/enhanced-user-notifications.md) du document [notifications utilisateur améliorées](~/ios/platform/user-notifications/enhanced-user-notifications.md) .

Lorsqu’une notification utilisateur avec une interface utilisateur personnalisée est présentée à l’utilisateur, elle dispose des éléments suivants :

[![Notification de l’utilisateur avec des éléments d’interface utilisateur personnalisés](advanced-user-notifications-images/customui01.png)](advanced-user-notifications-images/customui01.png#lightbox)

Si l’utilisateur interagit avec les actions personnalisées (présentées sous la notification), l’interface utilisateur peut être mise à jour pour que les commentaires de l’utilisateur soient ceux qui se sont produits lors de l’appel d’une action donnée.

### <a name="adding-a-notification-content-extension"></a>Ajout d’une extension de contenu de notification

Pour implémenter une interface utilisateur de notification utilisateur personnalisée dans une application Xamarin. iOS, procédez comme suit :

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez la solution de l’application dans Visual Studio pour Mac.
2. Cliquez avec le bouton droit sur le nom de la solution dans le **panneau solutions** puis sélectionnez **Ajouter**  >  **Ajouter un nouveau projet**.
3. Sélectionnez extensions **iOS**  >  **Extensions**  >  de**contenu de notification** , puis cliquez sur le bouton **suivant** : 

    [![Sélectionner les extensions de contenu de notification](advanced-user-notifications-images/notify01.png)](advanced-user-notifications-images/notify01.png#lightbox)
4. Entrez un **nom** pour l’extension et cliquez sur le bouton **suivant** : 

    [![Entrer un nom pour l’extension](advanced-user-notifications-images/notify02.png)](advanced-user-notifications-images/notify02.png#lightbox)
5. Ajustez le **nom du projet** et/ou le nom de la **solution** , si nécessaire, puis cliquez sur le bouton **créer** : 

    [![Ajuster le nom du projet et/ou le nom de la solution](advanced-user-notifications-images/notify03.png)](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Ouvrez la solution de l’application dans Visual Studio pour Mac.
2. Cliquez avec le bouton droit sur le nom de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter > nouveau projet...**.
3. Sélectionnez **Visual C# > extensions iOS > extension de contenu de notification**:

    [![Sélectionner les extensions de contenu de notification](advanced-user-notifications-images/notify01.w157-sml.png)](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. Entrez un **nom** pour l’extension et cliquez sur le bouton **OK** .

-----

Lorsque l’extension de contenu de notification est ajoutée à la solution, trois fichiers sont créés dans le projet de l’extension :

1. `NotificationViewController.cs`-Il s’agit du contrôleur d’affichage principal pour l’extension de contenu de notification.
2. `MainInterface.storyboard`-Où le développeur dispose de l’interface utilisateur visible pour l’extension de contenu de notification dans le concepteur iOS.
3. `Info.plist`-Contrôle la configuration de l’extension de contenu de notification.

Le fichier par défaut se `NotificationViewController.cs` présente comme suit :

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

        }
        #endregion
    }
}
```

La `DidReceiveNotification` méthode est appelée lorsque la notification est développée par l’utilisateur afin que l’extension de contenu de notification puisse remplir l’interface utilisateur personnalisée avec le contenu du `UNNotification` . Dans l’exemple ci-dessus, une étiquette a été ajoutée à la vue, exposée au code portant le nom `label` et utilisée pour afficher le corps de la notification.

### <a name="setting-the-notification-content-extensions-categories"></a>Définition des catégories de l’extension de contenu de notification

Le système doit être informé de la recherche de l’extension de contenu de notification de l’application en fonction des catégories spécifiques auxquelles elle répond. Effectuez les actions suivantes :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le fichier de l’extension `Info.plist` dans le **panneau solutions** pour l’ouvrir et le modifier.
2. Basculez sur la vue **Source**.
3. Développez la `NSExtension` clé.
4. Ajoutez la `UNNotificationExtensionCategory` clé en tant que **chaîne** de type avec la valeur de la catégorie à laquelle l’extension appartient (dans cet exemple, « événement-invitation ») : 

    [![Ajouter la clé UNNotificationExtensionCategory](advanced-user-notifications-images/customui02.png)](advanced-user-notifications-images/customui02.png#lightbox)
5. Enregistrez vos modifications.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le fichier de l’extension `Info.plist` dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
2. Développez la `NSExtension` clé.
3. Ajoutez la `UNNotificationExtensionCategory` clé en tant que **chaîne** de type avec la valeur de la catégorie à laquelle l’extension appartient (dans cet exemple, « événement-invitation ») : 

    [![Ajouter la clé UNNotificationExtensionCategory](advanced-user-notifications-images/customui02w.png)](advanced-user-notifications-images/customui02w.png#lightbox)
4. Enregistrez vos modifications.

-----

Les catégories d’extension de contenu de notification ( `UNNotificationExtensionCategory` ) utilisent les mêmes valeurs de catégorie que celles utilisées pour enregistrer les actions de notification. Dans le cas où l’application utilisera la même interface utilisateur pour plusieurs catégories, basculez `UNNotificationExtensionCategory` vers le **tableau** de types et fournissez toutes les catégories requises. Par exemple :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Catégories d’extension de contenu de notification](advanced-user-notifications-images/customui03.png)](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Catégories d’extension de contenu de notification](advanced-user-notifications-images/customui03w.png)](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Masquage du contenu de notification par défaut

Dans le cas où l’interface utilisateur de notification personnalisée affichera le même contenu que la notification par défaut (titre, sous-titre et corps affiché automatiquement au bas de l’interface utilisateur de notification), vous pouvez masquer ces informations par défaut en ajoutant la `UNNotificationExtensionDefaultContentHidden` clé à la `NSExtensionAttributes` clé en tant que type **booléen** avec la valeur `YES` dans le fichier de l’extension `Info.plist` :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Recherche d’informations par défaut](advanced-user-notifications-images/customui04.png)](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Recherche d’informations par défaut](advanced-user-notifications-images/customui04w.png)](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Conception de l’interface utilisateur personnalisée

Pour concevoir l’interface utilisateur personnalisée de l’extension de contenu de notification, double-cliquez sur le `MainInterface.storyboard` fichier pour l’ouvrir et le modifier dans le concepteur iOS, faites glisser les éléments dont vous avez besoin pour créer l’interface souhaitée (comme `UILabels` et `UIImageViews` ).

> [!NOTE]
> Depuis iOS 12, une extension de contenu de notification peut inclure des contrôles interactifs tels que des boutons et des champs de texte. Pour plus d’informations, consultez la documentation [interactive notifications dans IOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) .

Une fois l’interface utilisateur disposée et les contrôles nécessaires exposés au code C#, ouvrez `NotificationViewController.cs` pour la modification et modifiez la `DidReceiveNotification` méthode pour remplir l’interface utilisateur lorsque l’utilisateur développe la notification. Par exemple :

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

        }
        #endregion
    }
}
```

### <a name="setting-the-content-area-size"></a>Définition de la taille de la zone de contenu

Pour ajuster la taille de la zone de contenu affichée à l’utilisateur, le code ci-dessous affecte `PreferredContentSize` à la propriété de la `ViewDidLoad` méthode la taille souhaitée. Cette taille peut également être ajustée en appliquant des contraintes à la vue dans le concepteur iOS, il est laissé au développeur de choisir la méthode qui leur convient le mieux.

Étant donné que le système de notification est déjà en cours d’exécution avant l’appel de l’extension de contenu de notification, la zone de contenu commence à la taille complète et est animée jusqu’à la taille demandée lorsqu’elle est présentée à l’utilisateur.

Pour éliminer cet effet, modifiez le `Info.plist` fichier pour l’extension et définissez la `UNNotificationExtensionInitialContentSizeRatio` clé de la `NSExtensionAttributes` clé pour taper **nombre** avec une valeur représentant le rapport souhaité. Par exemple :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Clé UNNotificationExtensionInitialContentSizeRatio](advanced-user-notifications-images/customui05.png)](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Clé UNNotificationExtensionInitialContentSizeRatio](advanced-user-notifications-images/customui05w.png)](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>Utilisation de pièces jointes dans l’interface utilisateur personnalisée

Étant donné que les pièces jointes de média (comme indiqué dans la section [Ajout de pièces jointes multimédias](#adding-media-attachments) ci-dessus) font partie de la charge utile de notification, elles sont accessibles et affichées dans l’extension de contenu de notification de la même manière que dans l’interface utilisateur de notification par défaut.

Par exemple, si l’interface utilisateur personnalisée ci-dessus comprenait un `UIImageView` qui était exposé à du code C#, le code suivant pouvait être utilisé pour le remplir à partir de la pièce jointe de média :

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }
        #endregion
    }
}
```

La pièce jointe multimédia étant gérée par le système, elle est en dehors du bac à sable (sandbox) de l’application. L’extension doit informer le système qu’elle souhaite accéder au fichier en appelant la `StartAccessingSecurityScopedResource` méthode. Lorsque l’extension est effectuée avec le fichier, elle doit appeler `StopAccessingSecurityScopedResource` pour libérer sa connexion.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Ajout d’actions personnalisées à une interface utilisateur personnalisée

Les boutons d’action personnalisés peuvent être utilisés pour ajouter de l’interactivité à une interface utilisateur de notification personnalisée. Pour plus d’informations sur les actions personnalisées, consultez la section [utilisation des actions de notification](~/ios/platform/user-notifications/enhanced-user-notifications.md) du document [notifications utilisateur améliorées](~/ios/platform/user-notifications/enhanced-user-notifications.md) .

En plus des actions personnalisées, l’extension de contenu de notification peut également répondre aux actions intégrées suivantes :

- **Action par défaut** : lorsque l’utilisateur clique sur une notification pour ouvrir l’application et afficher les détails de la notification donnée.
- **Ignorer l’action** : cette action est envoyée à l’application lorsque l’utilisateur ignore une notification donnée.

Les extensions de contenu de notification ont également la possibilité de mettre à jour leur interface utilisateur lorsque l’utilisateur appelle l’une des actions personnalisées, telles que l’émission d’une date comme acceptée quand l’utilisateur appuie sur le bouton **accepter** une action personnalisée. En outre, les extensions de contenu de notification peuvent indiquer au système de différer le rejet de l’interface utilisateur de notification afin que l’utilisateur puisse voir l’effet de son action avant la fermeture de la notification.

Pour ce faire, implémentez une deuxième version de la `DidReceiveNotification` méthode qui comprend un gestionnaire d’achèvement. Par exemple :

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;
using CoreGraphics;

namespace myApp {
    public class NotificationViewController : UIViewController, UNNotificationContentExtension {

        public override void ViewDidLoad() {
            base.ViewDidLoad();

            // Adjust the size of the content area
            var size = View.Bounds.Size
            PreferredContentSize = new CGSize(size.Width, size.Width/2);
        }

        public void DidReceiveNotification(UNNotification notification) {

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = Content.UserInfo["location"] as string;
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments[0];
                if (attachment.Url.StartAccessingSecurityScopedResource()) {
                    EventImage.Image = UIImage.FromFile(attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch(response.ActionIdentifier){
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
    }
}
```

En ajoutant le `Server.PostEventResponse` Gestionnaire à la `DidReceiveNotification` méthode de l’extension de contenu de notification, l’extension *doit* gérer toutes les actions personnalisées. L’extension peut également transférer les actions personnalisées sur l’application conteneur en modifiant le `UNNotificationContentExtensionResponseOption` . Par exemple :

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Utilisation de l’action d’entrée de texte dans l’interface utilisateur personnalisée

Selon la conception de l’application et de la notification, il peut arriver que l’utilisateur entre du texte dans la notification (par exemple, la réponse à un message). Une extension de contenu de notification a accès à l’action d’entrée de texte intégrée tout comme une notification standard.

Par exemple :

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Computed Properties
        // Allow to take input
        public override bool CanBecomeFirstResponder {
            get { return true; }
        }

        // Return the custom created text input view with the
        // required buttons and return here
        public override UIView InputAccessoryView {
            get { return InputView; }
        }
        #endregion

        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Private Methods
        private UNNotificationCategory MakeExtensionCategory ()
        {

            // Create Accept Action
            ...

            // Create decline Action
            ...

            // Create Text Input Action
            var commentID = "comment";
            var commentTitle = "Comment";
            var textInputButtonTitle = "Send";
            var textInputPlaceholder = "Enter comment here...";
            var commentAction = UNTextInputNotificationAction.FromIdentifier (commentID, commentTitle, UNNotificationActionOptions.None, textInputButtonTitle, textInputPlaceholder);

            // Create category
            var categoryID = "event-invite";
            var actions = new UNNotificationAction [] { acceptAction, declineAction, commentAction };
            var intentIDs = new string [] { };
            var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);

            // Return new category
            return category;

        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Is text input?
            if (response is UNTextInputNotificationResponse) {
                var textResponse = response as UNTextInputNotificationResponse;
                Server.Send (textResponse.UserText, () => {
                    // Close Notification
                    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
                });
            }

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch (response.ActionIdentifier) {
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
        #endregion
    }
}
```

Ce code crée une action d’entrée de texte et l’ajoute à la catégorie de l’extension (dans la `MakeExtensionCategory` méthode). Dans la `DidReceive` méthode override, il gère l’entrée de texte par l’utilisateur à l’aide du code suivant :

```csharp
// Is text input?
if (response is UNTextInputNotificationResponse) {
    var textResponse = response as UNTextInputNotificationResponse;
    Server.Send (textResponse.UserText, () => {
        // Close Notification
        completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
    });
}
```

Si la conception appelle pour ajouter des boutons personnalisés au champ d’entrée de texte, ajoutez le code suivant pour les inclure :

```csharp
// Allow to take input
public override bool CanBecomeFirstResponder {
    get {return true;}
}

// Return the custom created text input view with the
// required buttons and return here
public override UIView InputAccessoryView {
    get {return InputView;}
}
```

Lorsque l’action de commentaire est déclenchée par l’utilisateur, le contrôleur d’affichage et le champ d’entrée de texte personnalisé doivent être activés :

```csharp
// Update UI when the user interacts with the
// Notification
Server.PostEventResponse += (response) {
    // Take action based on the response
    switch(response.ActionIdentifier){
    ...
    case "comment":
        BecomeFirstResponder();
        TextField.BecomeFirstResponder();
        break;
    }

    // Close Notification
    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);

};
```

## <a name="summary"></a>Résumé

Cet article a pris une vue avancée de l’utilisation de la nouvelle infrastructure de notification utilisateur dans une application Xamarin. iOS. Elle a abordé l’ajout de pièces jointes à la notification locale et à distance, et elle est traitée à l’aide de la nouvelle interface utilisateur de notification pour créer des interfaces utilisateur de notification personnalisées.

## <a name="related-links"></a>Liens connexes

- [Exemples iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Référence du Framework UserNotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guide de programmation des notifications locales et distantes](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
