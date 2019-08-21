---
title: Résolution des problèmes surveilléos
description: Ce document aborde les problèmes connus et les solutions de contournement pour le développement de Watchos avec Xamarin. Il décrit les images avec des problèmes, en ajoutant manuellement des fichiers de contrôleur d’interface, en lançant une application espion à partir de la ligne de commande, et bien plus encore.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 4ce217d31811df82f0779a22d0c64464b5295fcc
ms.sourcegitcommit: 3434624a36a369986b6aeed7959dae60f7112a14
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69629657"
---
# <a name="watchos-troubleshooting"></a>Résolution des problèmes surveilléos

Cette page contient des informations supplémentaires et des solutions de contournement pour les problèmes que vous pouvez rencontrer.

- [Problèmes connus](#knownissues)

- [Suppression du canal alpha des images d’icône](#noalpha)

- [Ajout manuel des fichiers de contrôleur d’interface](#add) pour XCode Interface Builder.

- [Lancement de WatchApp à partir de la ligne de commande](#command_line).

<a name="knownissues" />

## <a name="known-issues"></a>Problèmes connus

### <a name="general"></a>Généralités

<a name="deploy" />

- Les versions antérieures de Visual Studio pour Mac afficher de manière incorrecte l’une des icônes **AppleCompanionSettings** comme 88x88 pixels; ce qui entraîne une **erreur d’icône manquante** si vous tentez de l’envoyer à l’App Store.
    Cette icône doit être 87x87 pixels (29 unités pour **@3x** les écrans de retine). Vous ne pouvez pas résoudre ce problème dans Visual Studio pour Mac: modifiez la ressource d’image dans Xcode ou modifiez manuellement le fichier **Contents. JSON** (pour qu’il corresponde à [cet exemple](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Si l' **ID d’offre groupée info. plist** du projet d’Extension Watch > n’est pas [correctement défini](~/ios/watchos/get-started/project-references.md) pour correspondre à l' **ID d’offre groupée**de l’application espion, le débogueur ne parvient pas à se connecter et Visual Studio pour Mac attend le message *«en attente du débogueur se connecter»* .

- Le débogage est pris en charge en mode notifications, mais peut ne pas être fiable. Une nouvelle tentative peut parfois fonctionner. Vérifiez que le fichier **info. plist** `WKCompanionAppBundleIdentifier` de l’application Watch correspond à l’identificateur de Bundle de l’application parent/conteneur iOS (par ex., celle qui s’exécute sur l’iPhone).

- le concepteur iOS n’affiche pas de flèches de point d’entrée pour l’aperçu ou les contrôleurs d’interface de notification.

- Vous ne pouvez pas `WKNotificationControllers` en ajouter deux à une table de montage séquentiel.
    Solution de contournement : L' `notificationCategory` élément dans le fichier XML de la table de montage séquentiel `id`est toujours inséré avec le même. Pour contourner ce problème, vous pouvez ajouter deux ou plusieurs contrôleurs de notification, ouvrir le fichier de plan conceptuel dans un éditeur de texte, `id` puis modifier manuellement l’élément pour qu’il soit unique.

    [![](troubleshooting-images/duplicate-id-sml.png "Ouverture du fichier de table de montage séquentiel dans un éditeur de texte et modification manuelle de l’élément ID pour qu’il soit unique")](troubleshooting-images/duplicate-id.png#lightbox)

- Une erreur «l’application n’a pas été générée» peut s’afficher lors de la tentative de lancement de l’application. Cela se produit après un **nettoyage** lorsque le projet de démarrage est défini sur le projet d’extension Watch.
    Le correctif consiste à sélectionner **générer > régénérer tout** , puis à relancer l’application.

### <a name="visual-studio"></a>Visual Studio

La prise en charge du concepteur iOS pour le kit de surveillance *requiert* la configuration correcte de la solution. Si les références de projet ne sont pas définies (voir [How to set References](~/ios/watchos/get-started/project-references.md)), l’aire de conception ne fonctionnera pas correctement.

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Suppression du canal alpha des images d’icône

Les icônes ne doivent pas contenir de canal alpha (le canal alpha définit des zones transparentes d’une image). dans le cas contraire, l’application est rejetée lors de l’envoi de l’App Store avec une erreur semblable à celle-ci:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Il est facile de supprimer le canal alpha sur Mac OS X à l’aide de l’application en version **préliminaire** :

1. Ouvrez l’image d’icône dans l' **Aperçu** , puis choisissez **fichier > Exporter**.

2. La boîte de dialogue qui s’affiche contient une case à cocher **alpha** si un canal alpha est présent.

    ![](troubleshooting-images/remove-alpha-sml.png "La boîte de dialogue qui s’affiche contient une case à cocher alpha si un canal alpha est présent.")

3. Décochez la case **alpha** et **Enregistrez** le fichier à l’emplacement approprié.

4. L’image d’icône doit maintenant passer les contrôles de validation d’Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Ajout manuel des fichiers du contrôleur d’interface

> [!IMPORTANT]
> La prise en charge de WatchKit par Xamarin comprend la conception des storyboards de Watch dans le concepteur iOS (dans Visual Studio pour Mac et Visual Studio), ce qui ne nécessite pas les étapes décrites ci-dessous. Donnez simplement un nom de classe à un contrôleur d’interface dans le panneau Propriétés C# du Visual Studio pour Mac et les fichiers de code seront créés automatiquement.


*Si* vous utilisez Xcode Interface Builder, procédez comme suit pour créer de nouveaux contrôleurs d’interface pour votre application Watch et activer la synchronisation avec Xcode afin que les prises et les C#actions soient disponibles dans:

1. Ouvrez l’interface de l’application Watch **. Storyboard** dans **Xcode Interface Builder**.

    ![](troubleshooting-images/add-6.png "Ouverture de la table de montage séquentiel dans Xcode Interface Builder")

2. Faites glisser un `InterfaceController` nouveau sur le Storyboard:

    ![](troubleshooting-images/add-1.png "Un InterfaceController")

3. Vous pouvez maintenant faire glisser des contrôles sur le contrôleur d’interface (par exemple, étiquettes et boutons), mais vous ne pouvez pas créer de prises ou d’actions pour l’instant, car il n’y a aucun fichier d’en-tête **. h** . Les étapes suivantes entraînent la création du fichier d’en-tête **. h** requis.

    ![](troubleshooting-images/add-2.png "Un bouton dans la disposition")

4. Fermez le Storyboard et revenez à Visual Studio pour Mac. Créez un nouveau C# fichier **MyInterfaceController.cs** (ou le nom de votre choix) dans le projet d’extension de l' **application Watch** (et non l’application Watch elle-même où se trouve la table de montage séquentiel). Ajoutez le code suivant (mise à jour de l’espace de noms, ClassName et du nom du constructeur):

    ```csharp
    using System;
    using WatchKit;
    using Foundation;

    namespace WatchAppExtension  // remember to update this
    {
        public partial class MyInterfaceController // remember to update this
        : WKInterfaceController
        {
            public MyInterfaceController // remember to update this
            (IntPtr handle) : base (handle)
            {
            }
            public override void Awake (NSObject context)
            {
                base.Awake (context);
                // Configure interface objects here.
                Console.WriteLine ("{0} awake with context", this);
            }
            public override void WillActivate ()
            {
                // This method is called when the watch view controller is about to be visible to the user.
                Console.WriteLine ("{0} will activate", this);
            }
            public override void DidDeactivate ()
            {
                // This method is called when the watch view controller is no longer visible to the user.
                Console.WriteLine ("{0} did deactivate", this);
            }
        }
    }
    ```

5. Créez un autre C# fichier **MyInterfaceController.Designer.cs** dans le projet d’extension de l' **application Watch** et ajoutez le code ci-dessous. Veillez à mettre à jour l’espace de noms, `Register` le ClassName et l’attribut:

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;

    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```

    > [!TIP]
    > Vous pouvez (si vous le souhaitez) faire de ce fichier un nœud enfant du premier fichier en le faisant glisser vers C# l’autre fichier dans le panneau solutions Visual Studio pour Mac. Elle s’affiche alors comme suit:

    ![](troubleshooting-images/add-5.png "Panneau solutions")

6. Sélectionnez **Générer > générer tout** afin que la synchronisation Xcode reconnaisse la nouvelle classe ( `Register` via l’attribut) que nous avons utilisée.

7. Rouvrez la table de montage séquentiel en cliquant avec le bouton droit sur le fichier d’animation de l’application Watch, puis en sélectionnant **Ouvrir avec > Xcode Interface Builder**:

    ![](troubleshooting-images/add-6.png "Ouverture de la table de montage séquentiel dans Interface Builder")

8. Sélectionnez votre nouveau contrôleur d’interface et donnez-lui le ClassName que vous avez défini ci-dessus, par exemple. `MyInterfaceController`.
    Si tout fonctionne correctement, il doit apparaître automatiquement dans la liste déroulante **classe:** et vous pouvez le sélectionner à partir de là.

    ![](troubleshooting-images/add-4.png "Définition d’une classe personnalisée")

9. Choisissez la vue de l’éditeur de l' **Assistant** dans Xcode (l’icône avec deux cercles se chevauchant) pour que vous puissiez voir la table de montage séquentiel et le code côte à côte:

    ![](troubleshooting-images/add-7.png "L’élément de barre d’outils Éditeur de l’Assistant")

    Lorsque le focus se trouve dans le volet de code, vérifiez que vous examinez le fichier d’en-tête **. h** et, si vous ne cliquez pas avec le bouton droit dans la barre de navigation et sélectionnez le fichier approprié (**MyInterfaceController. h**).

    ![](troubleshooting-images/add-8.png "Sélectionner MyInterfaceController")

10. Vous pouvez maintenant créer des prises et des actions en appuyant sur **CTRL + déplacer** du storyboard dans le fichier d’en-tête **. h** .

    ![](troubleshooting-images/add-9.png "Création des sorties et des actions")

    Lorsque vous relâchez le glissement, vous êtes invité à indiquer si vous souhaitez créer une sortie ou une action, et choisir son nom:

    ![](troubleshooting-images/add-a.png "Boîte de dialogue sortie et action")

11. Une fois que les modifications de la table de montage séquentiel sont enregistrées et que Xcode est fermé, revenez à Visual Studio pour Mac. Il détecte les modifications du fichier d’en-tête et ajoute automatiquement le code au fichier **. Designer.cs** :

    ```csharp
    [Register ("MyInterfaceController")]
    partial class MyInterfaceController
    {
        [Outlet]
        WatchKit.WKInterfaceButton myButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (myButton != null) {
                myButton.Dispose ();
                myButton = null;
            }
        }
    }
    ```

Vous pouvez maintenant référencer le contrôle (ou implémenter l’action C#) dans!


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>Lancement de l’application Watch à partir de la ligne de commande

> [!IMPORTANT]
> Vous pouvez démarrer l’application espion en mode application normale par défaut, ainsi qu’en mode **Aperçu** ou en mode **notification** à l’aide de [paramètres d’exécution personnalisés](~/ios/watchos/get-started/installation.md#custommodes) dans Visual Studio pour Mac et Visual Studio.


Vous pouvez également utiliser la ligne de commande pour contrôler le simulateur iOS. L’outil en ligne de commande utilisé pour lancer des applications de surveillance est **mTouch**.

Voici un exemple complet (exécuté comme une seule ligne dans le terminal):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

Le paramètre que vous devez mettre à jour pour refléter votre `launchsimwatch`application est le suivant:

### <a name="--launchsimwatch"></a>--launchsimwatch

Le chemin d’accès complet au bundle d’applications principal *pour l’application IOS qui contient l’application espion et l’extension*.

> [!NOTE]
> Le chemin d’accès que vous devez fournir concerne le *fichier application iPhone. app*, c’est-à-dire celui qui sera déployé sur le simulateur iOS et qui contient à la fois l’extension Watch et l’application Watch.

Exemple :

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>Mode de notification

Pour tester le mode de [ **notification** ](~/ios/watchos/platform/notifications.md)de l’application, `watchlaunchmode` affectez `Notification` au paramètre la valeur et fournissez un chemin d’accès à un fichier JSON qui contient une charge utile de notification de test.

Le paramètre de charge utile est *requis* pour le mode de notification.

Par exemple, ajoutez ces arguments à la commande mTouch:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>Autres arguments

Les arguments restants sont expliqués ci-dessous:

### <a name="--sdkroot"></a>--SDKRoot

Requis. Spécifie le chemin d’accès à Xcode (6,2 ou version ultérieure).

Exemple :

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--appareil

Appareil simulateur à exécuter. Cela peut être spécifié de deux façons, soit à l’aide de l’UDID d’un appareil spécifique, soit à l’aide d’une combinaison de type d’exécution et d’appareil.

Les valeurs exactes varient d’un ordinateur à l’autre et peuvent être interrogées à l’aide de l’outil **simctl** d’Apple:

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

Exemple :

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**Runtime et type d’appareil**

Exemple :

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
