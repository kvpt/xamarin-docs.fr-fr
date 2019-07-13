---
title: Notifications utilisateur avancées dans Xamarin.iOS
description: Cet article examine plus approfondie l’infrastructure de Notifications à l’utilisateur, introduite dans iOS 10. Il aborde les notifications à l’utilisateur, l’interface utilisateur de notification, médias joints, des interfaces utilisateur personnalisées et bien plus encore.
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 255603eefb4d7cfd3b906e1744aa19da6a77259a
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865293"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Notifications utilisateur avancées dans Xamarin.iOS

Nouveau à iOS 10, la Notification utilisateur framework permet de la fourniture et la gestion des notifications locales et distantes. À l’aide de cette infrastructure, une application ou une extension d’application permettre planifier la remise des notifications locales en spécifiant un ensemble de conditions comme emplacement ou de l’heure.

## <a name="about-user-notifications"></a>À propos des notifications utilisateur

La nouvelle infrastructure de Notification à l’utilisateur permet de la fourniture et la gestion des notifications locales et distantes. À l’aide de cette infrastructure, une application ou une Extension d’application permettre planifier la remise des notifications locales en spécifiant un ensemble de conditions comme emplacement ou de l’heure.

En outre, l’application ou une extension peut s’afficher (et éventuellement modifier) notifications locales et distantes comme ils sont remis à l’appareil iOS de l’utilisateur.

La nouvelle infrastructure de l’interface utilisateur de la Notification utilisateur permet à une application ou une Extension d’application pour personnaliser l’apparence des notifications locales et distantes quand elles sont présentées à l’utilisateur.

Cette infrastructure fournit les méthodes suivantes qui une application peut fournir des notifications à un utilisateur :

- **Alertes Visual** - où reporte la notification à partir du haut de l’écran sous forme de bannière.
- **Vibrations et son** -peut être associé à une notification.
- **Icône application un badge** : si l’icône de l’application affiche un badge indique que le nouveau contenu est disponible. Comme le nombre de messages électroniques non lus.

En outre, selon le contexte de l’utilisateur actuel, il existe différentes façons dont une notification s’affiche :

- Si l’appareil est déverrouillé, la notification sera généraliser à partir du haut de l’écran sous forme de bannière.
- Si l’appareil est verrouillé, la notification s’affichera sur l’écran de verrouillage de l’utilisateur.
- Si l’utilisateur a manqué une notification, ils peuvent ouvrir le centre de Notification et afficher les notifications en attente disponibles, il.

Une application Xamarin.iOS a deux types de Notifications à l’utilisateur qu’il est en mesure d’envoyer :

- **Notifications locales** -ceux-ci sont envoyés par les applications installées localement sur l’appareil de l’utilisateur.
- **Notifications à distance** - sont envoyés à partir d’un serveur distant et soit présenté à l’utilisateur ou déclenche une mise à jour d’arrière-plan du contenu de l’application.

Pour plus d’informations, consultez notre [Notifications améliorées à l’utilisateur](~/ios/platform/user-notifications/enhanced-user-notifications.md) documentation.

## <a name="the-new-user-notification-interface"></a>La nouvelle interface de notification utilisateur

Notifications à l’utilisateur dans iOS 10 sont présentées avec une nouvelle conception de l’interface utilisateur qui fournit davantage de contenu comme un titre, un sous-titre et un facultatif des pièces jointes de média peut être présenté sur l’écran de verrouillage, sous forme de bannière en haut de l’appareil ou dans le centre de notifications.

Peu importe où un utilisateur s’affiche dans iOS 10, il est présenté avec le même aspect et les mêmes fonctionnalités.

Dans iOS 8, Apple a introduit les Notifications actionnables où le développeur peut joindre des Actions personnalisées à une Notification et autoriser l’utilisateur à effectuer une opération sur une Notification sans avoir à lancer l’application. Dans iOS 9, Apple améliorée des Notifications exploitables avec réponse rapide, ce qui permet à l’utilisateur répondre à une Notification avec l’entrée de texte.

Étant donné que les Notifications à l’utilisateur sont une partie intégrante de plus de l’expérience utilisateur dans iOS 10, Apple a étendue Notifications exploitables pour prendre en charge 3D Touch, où l’utilisateur appuie sur une notification et une interface utilisateur personnalisée s’affiche pour fournir une interaction riche avec la notification.

Lorsque l’interface utilisateur de Notification utilisateur personnalisée s’affiche, si l’utilisateur interagit avec toutes les Actions attachées à la Notification, l’interface utilisateur personnalisée peut être instantanément mise à jour pour fournir des commentaires concernant ce qui a été changé.

Nouveau à iOS 10, l’API de l’interface utilisateur de Notification utilisateur permet à une application Xamarin.iOS pour facilement tirer parti de ces nouvelles fonctionnalités de l’interface utilisateur de la Notification utilisateur.

## <a name="adding-media-attachments"></a>Ajout de pièces jointes de média

Un des éléments plus courantes qui obtient partagés entre les utilisateurs étant photos, iOS 10 ajouté la possibilité de joindre un élément multimédia (par exemple, une photo) directement à une Notification, où il sera présenté et facilement accessibles à l’utilisateur avec le reste des conte de la Notification NT.

Toutefois, en raison des tailles de l’envoi de même une petite image, attacher à une charge utile de Notification à distance devient difficile. Pour gérer cette situation, le développeur peut utiliser la nouvelle Extension de Service dans iOS 10 pour télécharger l’image à partir d’une autre source (par exemple, une banque de données CloudKit) et l’attacher au contenu de la notification avant qu’il est affiché à l’utilisateur.

Pour obtenir une Notification à distance doivent être modifiées par une Extension de Service, sa charge utile doit être marqué comme mutable. Par exemple :

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

Examinons la présentation suivante du processus :

[![](advanced-user-notifications-images/extension02.png "Processus d’ajout des pièces jointes de média")](advanced-user-notifications-images/extension02.png#lightbox)

Une fois que la Notification à distance est envoyée à l’appareil (via APNs), l’Extension de Service peut ensuite télécharger l’image requis par le biais de n’importe quel signifie que vous le souhaitez (par exemple un `NSURLSession`) et après avoir reçu l’image, il peut modifier le contenu de la Notification et l’affichage Il est à l’utilisateur.

Voici un exemple de comment ce processus peut être géré dans le code :

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

Lorsque la Notification est reçue à partir de l’APNs, l’adresse de l’image personnalisée est lu à partir du contenu et le fichier est téléchargé à partir du serveur. Un `UNNotificationAttachement` est créée avec un ID unique et l’emplacement local de l’image (comme un `NSUrl`). Une copie mutable de contenu de Notification est créée et médias joints sont ajoutés. Enfin, la Notification est affichée à l’utilisateur en appelant le `contentHandler`.

Une fois qu’une pièce jointe a été ajoutée à une Notification, le système est sur le déplacement et la gestion du fichier.

Outre les Notifications à distance présentées ci-dessus, à laquelle les médias joints sont également prises en charge à partir des Notifications locales, où le `UNNotificationAttachement` est créé et attaché à la Notification, ainsi que son contenu.

Notification dans iOS 10 prend en charge des médias joints d’images (statique et des images GIF), audio ou vidéo et le système seront affiche automatiquement l’interface utilisateur personnalisée correcte pour chacun de ces types de pièces jointes lorsque la Notification est présentée à l’utilisateur.

> [!NOTE]
> Soyez vigilant pour optimiser la taille du média et le temps que nécessaire pour télécharger le support à partir du serveur distant (ou pour assembler le support pour les Notifications locales) en tant que le système impose des limites strictes pour les deux lors de l’Extension de l’application de Service en cours d’exécution. Par exemple, envisagez d’envoyer une version mise à l’échelle vers le bas de l’image ou un clip minuscule d’une vidéo qui sera présenté dans la Notification.

## <a name="creating-custom-user-interfaces"></a>Création d’interfaces utilisateur personnalisées

Pour créer une Interface utilisateur personnalisée pour ses Notifications à l’utilisateur, le développeur doit ajouter une Extension de contenu de Notification (nouveau à iOS 10) à la solution de l’application.

L’Extension de contenu de Notification permet au développeur d’ajouter leurs propres vues dans l’interface utilisateur de Notification et de dessiner de n’importe quel contenu qu’ils souhaitent. Extensions de contenu de Notification compter d’iOS 12, prennent en charge les contrôles d’interface utilisateur interactives tels que des boutons et des curseurs. Pour plus d’informations, consultez le [notifications interactives dans iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) documentation.

Pour prendre en charge l’interaction utilisateur avec une Notification de l’utilisateur, Actions personnalisées doit être créées, inscrit auprès du système et joint à la Notification avant de planifier avec le système. L’Extension de contenu de Notification sera appelée pour gérer le traitement de ces actions. Consultez le [l’utilisation d’Actions de Notification](~/ios/platform/user-notifications/enhanced-user-notifications.md) section de la [Notifications améliorées à l’utilisateur](~/ios/platform/user-notifications/enhanced-user-notifications.md) document pour plus d’informations sur les Actions personnalisées.

Lorsqu’une Notification de l’utilisateur avec une interface utilisateur personnalisée est présentée à l’utilisateur, il aura les éléments suivants :

[![](advanced-user-notifications-images/customui01.png "Une Notification de l’utilisateur avec un élément de l’interface utilisateur personnalisée")](advanced-user-notifications-images/customui01.png#lightbox)

Si l’utilisateur interagit avec les Actions personnalisées (présentée ci-dessous la Notification), l’Interface utilisateur peuvent être mis à jour pour fournir une rétroaction comme le que s’est-il passé lorsqu’ils appelé une action donnée.

### <a name="adding-a-notification-content-extension"></a>Ajout d’une extension de contenu de notification

Pour implémenter une interface de Notification utilisateur personnalisé dans une application Xamarin.iOS, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez la solution de l’application dans Visual Studio pour Mac.
2. Avec le bouton droit sur le nom de la Solution dans le **panneau solutions** et sélectionnez **ajouter** > **ajouter un nouveau projet**.
3. Sélectionnez **iOS** > **Extensions** > **Extensions contenu Notification** et cliquez sur le **suivant** bouton : 

    [![](advanced-user-notifications-images/notify01.png "Sélectionnez les Extensions de contenu de Notification")](advanced-user-notifications-images/notify01.png#lightbox)
4. Entrez un **nom** pour l’extension et cliquez sur le **suivant** bouton : 

    [![](advanced-user-notifications-images/notify02.png "Entrez un nom pour l’extension")](advanced-user-notifications-images/notify02.png#lightbox)
5. Ajuster la **nom_projet** et/ou **nom de la Solution** si nécessaire et cliquez sur le **créer** bouton : 

    [![](advanced-user-notifications-images/notify03.png "Ajuster le nom du projet et/ou le nom de la Solution")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Ouvrez la solution de l’application dans Visual Studio pour Mac.
2. Avec le bouton droit sur le nom de la Solution dans le **l’Explorateur de solutions** et sélectionnez **Ajouter > Nouveau projet...** .
3. Sélectionnez **Visual C# > Extensions iOS > Extension de contenu de Notification**:

    [![](advanced-user-notifications-images/notify01.w157-sml.png "Sélectionnez les Extensions de contenu de Notification")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. Entrez un **nom** pour l’extension et cliquez sur le **OK** bouton.

-----

Lors de l’Extension de contenu de Notification est ajoutée à la solution, trois fichiers seront créés dans les projets de l’Extension :

1. `NotificationViewController.cs` -Il s’agit du contrôleur d’affichage principal pour l’Extension de contenu de Notification.
2. `MainInterface.storyboard` -Où le développeur présente l’interface utilisateur visible pour l’Extension de contenu de Notification dans le concepteur iOS.
3. `Info.plist` -Contrôle la configuration de l’Extension de contenu de Notification.

La valeur par défaut `NotificationViewController.cs` fichier ressemble à ceci :

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

Le `DidReceiveNotification` méthode est appelée lorsque la Notification est développée par l’utilisateur afin que l’Extension de contenu de Notification peut remplir l’interface utilisateur personnalisée avec le contenu de la `UNNotification`. Dans l’exemple ci-dessus, une étiquette a été ajoutée à la vue, exposée au code portant le nom `label` et est utilisé pour afficher le corps de la Notification.

### <a name="setting-the-notification-content-extensions-categories"></a>Définition des catégories de la Notification Extension de contenu

Le système doit être informé sur la façon de trouver l’Extension de contenu de Notification de l’application selon les catégories spécifiques, qu'il répond aux. Effectuez ce qui suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur l’Extension `Info.plist` de fichiers dans le **panneau solutions** à ouvrir pour modification.
2. Basculez vers le **Source** vue.
3. Développez le `NSExtension` clé.
4. Ajouter le `UNNotificationExtensionCategory` clé en tant que type **chaîne** avec la valeur de la catégorie de l’Extension appartient à (dans cet exemple ' invitation de l’événement) : 

    [![](advanced-user-notifications-images/customui02.png "Ajouter la clé UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02.png#lightbox)
5. Enregistrez les modifications apportées.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur l’Extension `Info.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
2. Développez le `NSExtension` clé.
3. Ajouter le `UNNotificationExtensionCategory` clé en tant que type **chaîne** avec la valeur de la catégorie de l’Extension appartient à (dans cet exemple ' invitation de l’événement) : 

    [![](advanced-user-notifications-images/customui02w.png "Ajouter la clé UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02w.png#lightbox)
4. Enregistrez les modifications apportées.

-----

Catégories de Extension de contenu de notification (`UNNotificationExtensionCategory`) utilisent les mêmes valeurs de catégorie sont utilisées pour enregistrer les Actions de Notification. Dans la situation où l’application utilisera la même interface utilisateur pour plusieurs catégories, basculez le `UNNotificationExtensionCategory` au type **tableau** et fournir toutes les catégories que nécessaires. Par exemple :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](advanced-user-notifications-images/customui03.png "Catégories d’Extension de contenu de notification")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui03w.png "Catégories d’Extension de contenu de notification")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Masquer le contenu de notification par défaut

Dans la situation où l’interface utilisateur de Notification personnalisé affichent le même contenu en tant que la valeur par défaut de Notification (titre, sous-titre et corps affichée automatiquement au bas de l’interface utilisateur de Notification), ces informations par défaut peuvent être masquées en ajoutant le `UNNotificationExtensionDefaultContentHidden`clés pour le `NSExtensionAttributes` clé en tant que type **booléenne** avec la valeur `YES` dans l’Extension `Info.plist` fichier :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](advanced-user-notifications-images/customui04.png "Recherche d’informations par défaut")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui04w.png "Recherche d’informations par défaut")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Conception de l’interface utilisateur personnalisée

Pour concevoir l’interface d’utilisateur personnalisée de l’Extension contenu Notification, double-cliquez sur le `MainInterface.storyboard` fichier à ouvrir pour modification dans le concepteur, iOS faire glisser les éléments dont vous avez besoin pour créer l’interface de votre choix (tel que `UILabels` et `UIImageViews`).

> [!NOTE]
> À compter d’iOS 12, une Extension de contenu de Notification peut inclure des contrôles interactifs tels que des boutons et champs de texte. Pour plus d’informations, consultez le [notifications interactives dans iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) documentation.

Une fois que l’interface utilisateur a été disposé et les contrôles nécessaires exposés à C# code, ouvrez le `NotificationViewController.cs` pour la modification et de modifier le `DidReceiveNotification` méthode pour remplir l’interface utilisateur lorsque l’utilisateur développe la notification. Par exemple :

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

Pour ajuster la taille de la zone de contenu affichée à l’utilisateur, le code ci-dessous définit les `PreferredContentSize` propriété dans le `ViewDidLoad` méthode à la taille souhaitée. Cette taille soit également ajustée en appliquant des contraintes à la vue dans le concepteur iOS, il incombe au développeur de choisir la méthode qui convient le mieux.

Étant donné que la Notification système est déjà en cours d’exécution avant la Notification d’Extension de contenu est appelée, la zone de contenu tout d’abord intégral en taille réelle et être animée jusqu'à la taille demandée lorsque présentées à l’utilisateur.

Pour éliminer cet effet, modifier la `Info.plist` fichier pour l’Extension et le jeu le `UNNotificationExtensionInitialContentSizeRatio` clé de la `NSExtensionAttributes` clé en type **nombre** avec une valeur qui représente le rapport souhaité. Par exemple :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](advanced-user-notifications-images/customui05.png "La clé UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui05w.png "La clé UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>À l’aide de médias joints dans l’interface utilisateur personnalisée

Étant donné que médias joints (comme indiqué dans le [Ajout médias joints](#adding-media-attachments) section ci-dessus) font partie de la charge utile de Notification, ils sont accessibles et affichés dans l’Extension de contenu de Notification, comme ils seraient dans la valeur par défaut Notification de l’interface utilisateur.

Par exemple, si l’interface utilisateur personnalisée de ci-dessus inclus un `UIImageView` qui ont été exposée au C# , écrivez le code suivant code pourrait être utilisé pour la remplir avec la pièce jointe de média :

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

Étant donné que la pièce jointe de média est gérée par le système, il est en dehors de bac à sable de l’application. L’extension doit indiquer au système qu’il veut l’accès au fichier en appelant le `StartAccessingSecurityScopedResource` (méthode). Lorsque l’extension est effectuée avec le fichier, il doit appeler la `StopAccessingSecurityScopedResource` pour libérer sa connexion.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Ajout d’actions personnalisées à une interface utilisateur personnalisée

Boutons d’action personnalisée peuvent être utilisés pour ajouter une interactivité à une interface utilisateur de Notification personnalisé. Consultez le [l’utilisation d’Actions de Notification](~/ios/platform/user-notifications/enhanced-user-notifications.md) section de la [Notifications améliorées à l’utilisateur](~/ios/platform/user-notifications/enhanced-user-notifications.md) document pour plus d’informations sur les actions personnalisées.

Outre les actions personnalisées, l’Extension de contenu de Notification peut répondre aux actions intégrées suivantes ainsi :

- **Action par défaut** -il s’agit quand l’utilisateur appuie sur une notification à l’application s’ouvre et affiche les détails de la notification donnée.
- **Faire disparaître Action** -cette action est envoyée à l’application lorsque l’utilisateur fermera une notification donnée.

Extensions de contenu de notification ont également la possibilité de mettre à jour leur interface utilisateur lorsque l’utilisateur appelle l’une des Actions personnalisées, telles que montrant une date comme accepté lorsque l’utilisateur appuie sur le **Accept** bouton d’Action personnalisée. En outre, les Extensions de contenu de Notification peut informer le système de retarder le rejet de l’interface utilisateur de Notification afin que l’utilisateur peut voir l’effet de leurs actions avant la fermeture de la Notification.

Cela est accompli en implémentant une deuxième version de la `DidReceiveNotification` méthode qui inclut un gestionnaire d’achèvement. Par exemple :

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

En ajoutant le `Server.PostEventResponse` gestionnaire à la `DidReceiveNotification` (méthode) de l’Extension de contenu de Notification, l’Extension *doit* gérer toutes les actions personnalisées. L’extension peut également transférer les actions personnalisées une session sur l’application de conteneur en modifiant le `UNNotificationContentExtensionResponseOption`. Par exemple :

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Utilisation de l’action d’entrée de texte dans l’interface utilisateur personnalisée

Selon la conception de l’application et la Notification, il peut arriver qui oblige l’utilisateur à entrer du texte dans la Notification (par exemple, en réponse à un message). Une Extension de contenu de Notification a accès à l’action d’entrée de texte intégré exactement comme une notification standard.

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

Ce code crée une nouvelle action d’entrée de texte et l’ajoute à la catégorie de l’Extension (dans le `MakeExtensionCategory`) méthode. Dans le `DidReceive` substituer la méthode, il gère l’utilisateur doive entrer du texte avec le code suivant :

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

Si les appels de conception pour l’ajout de boutons personnalisés pour le champ d’entrée de texte, ajoutez le code suivant pour les inclure :

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

## <a name="summary"></a>Récapitulatif

Cet article a examiné avancées à l’aide de la nouvelle infrastructure de Notification à l’utilisateur dans une application Xamarin.iOS. Il couvert l’ajout de pièces jointes de média Local et de Notification à distance et couvert à l’aide de la nouvelle interface de Notification utilisateur pour créer des interfaces utilisateur personnalisées de Notification.

## <a name="related-links"></a>Liens connexes

- [Exemples iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Référence de l’infrastructure Usernotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guide de programmation Notification locaux et distants](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
