---
title: Interfaces utilisateur des notifications interactives dans Xamarin. iOS
description: Avec iOS 12, il est possible de créer des interfaces utilisateur interactives pour les notifications locales et distantes. Ce guide explique comment utiliser ces fonctionnalités avec Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: e629cd8f481558991d02c7fb879502ebd54753bd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031938"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>Interfaces utilisateur des notifications interactives dans Xamarin. iOS

Les [extensions de contenu de notification](~/ios/platform/user-notifications/advanced-user-notifications.md), introduites dans iOS 10, permettent de créer des interfaces utilisateur personnalisées pour les notifications. À compter d’iOS 12, les interfaces utilisateur de notification peuvent contenir des éléments interactifs tels que des boutons et des curseurs.

## <a name="sample-app-redgreennotifications"></a>Exemple d’application : RedGreenNotifications

L’exemple d’application [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) contient une extension de contenu de notification avec une interface utilisateur interactive.

Les extraits de code de ce guide proviennent de cet exemple.

## <a name="notification-content-extension-infoplist-file"></a>Fichier info. plist de l’extension de contenu de notification

Dans l’exemple d’application, le fichier **info. plist** du projet **RedGreenNotificationsContentExtension** contient la configuration suivante :

```xml
<!-- ... -->
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>UNNotificationExtensionCategory</key>
        <array>
            <string>red-category</string>
            <string>green-category</string>
        </array>
        <key>UNNotificationExtensionUserInteractionEnabled</key>
        <true/>
        <key>UNNotificationExtensionDefaultContentHidden</key>
        <true/>
        <key>UNNotificationExtensionInitialContentSizeRatio</key>
        <real>0.6</real>
    </dict>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.usernotifications.content-extension</string>
    <key></key>
    <true/>
</dict>
<!-- ... -->
```

Notez les caractéristiques suivantes :

- Le tableau `UNNotificationExtensionCategory` spécifie le type des catégories de notification gérées par l’extension de contenu.
- Afin de prendre en charge le contenu interactif, l’extension de contenu de notification définit la clé de `UNNotificationExtensionUserInteractionEnabled` sur `true`.
- La clé de `UNNotificationExtensionInitialContentSizeRatio` spécifie le rapport hauteur/largeur initial pour l’interface de l’extension de contenu.

## <a name="interactive-interface"></a>Interface interactive

**MainInterface. Storyboard**, qui définit l’interface d’une extension de contenu de notification, est un storyboard standard contenant un contrôleur d’affichage unique. Dans l’exemple d’application, le contrôleur d’affichage est de type `NotificationViewController`et contient une vue d’image, trois boutons et un curseur. La table de montage séquentiel associe ces contrôles aux gestionnaires définis dans **NotificationViewController.cs**:

- Le gestionnaire de bouton **lancer l’application** appelle la méthode d’action `PerformNotificationDefaultAction` sur `ExtensionContext`, qui lance l’application :

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    Dans l’application, le `Delegate` du centre de notification de l’utilisateur (dans l’exemple d’application, le `AppDelegate`) peut répondre à l’interaction dans la méthode `DidReceiveNotificationResponse` :

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- Le gestionnaire de bouton **Fermer la notification** appelle `DismissNotificationContentExtension` sur `ExtensionContext`, ce qui ferme la notification :

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- Le gestionnaire de bouton **Supprimer la notification** ignore la notification et la supprime du centre de notifications :

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- La méthode qui gère les modifications de valeur sur le curseur met à jour l’alpha de l’image affichée dans l’interface de la notification :

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>Liens connexes

- [Exemple d’application – RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Infrastructure de notifications utilisateur dans Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Nouveautés des notifications utilisateur (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Meilleures pratiques et nouveautés dans les notifications utilisateur (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Notifications riches (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [Génération d’une notification à distance (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
