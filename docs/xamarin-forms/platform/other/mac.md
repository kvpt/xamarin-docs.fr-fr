---
title: Configuration de la plateforme Mac
description: Cet article explique comment ajouter un projet Mac à un Xamarin.Forms projet, qui produit une application capable de s’exécuter sur MacOS Sierra et MacOS El Capitan.
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 11897d2d3b8b7ba0a62956f1dbe4d8b873352e7a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139553"
---
# <a name="mac-platform-setup"></a>Configuration de la plateforme Mac

![PRÉVERSION](~/media/shared/preview.png)

Avant de commencer, créez (ou utilisez un projet existant) Xamarin.Forms . Vous pouvez uniquement ajouter des applications Mac à l’aide de Visual Studio pour Mac.

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**Ajout d’un projet macOS à une Xamarin.Forms vidéo**

## <a name="adding-a-mac-app"></a>Ajout d’une application Mac

Suivez ces instructions pour ajouter une application Mac qui s’exécutera sur macOS Sierra et macOS El Capitan :

1. Dans Visual Studio pour Mac, cliquez avec le bouton droit sur la solution existante, Xamarin.Forms puis choisissez **Ajouter > ajouter un nouveau projet...**

2. Dans la fenêtre **nouveau projet** , choisissez **Mac > application > cacao application** , puis appuyez sur **suivant**.

3. Tapez un **nom d’application** (et éventuellement un nom différent pour l’élément Dock), puis cliquez sur **suivant**.

4. Passez en revue la configuration et appuyez sur **créer**. Ces étapes sont présentées ci-dessous :

    ![Instructions animées montrant comment ajouter une application de cacao](mac-images/add-macos-proj.gif)

5. Dans le projet Mac, cliquez avec le bouton droit sur **packages > ajouter des packages...** pour ajouter [Xamarin.Forms](https://www.nuget.org/packages/Xamarin.Forms/) NuGet. Vous devez également mettre à jour les autres projets pour qu’ils utilisent la même version du Xamarin.Forms package NuGet.

6. Dans le projet Mac, cliquez avec le bouton droit sur **références** et ajoutez une référence au Xamarin.Forms projet (projet partagé ou projet de bibliothèque de .NET standard).

    ![Ajouter une référence au Xamarin.Forms projet de code partagé](mac-images/references-sml.png)

7. Mettez à jour **main.cs** pour initialiser le `AppDelegate` :

    ```csharp
    static class MainClass
    {
        static void Main(string[] args)
        {
            NSApplication.Init();
            NSApplication.SharedApplication.Delegate = new AppDelegate(); // add this line
            NSApplication.Main(args);
        }
    }
    ```

8. Mettez à jour `AppDelegate` pour initialiser Xamarin.Forms , créez une fenêtre et chargez l' Xamarin.Forms application (mémoriser pour définir un approprié `Title` ). _Si vous avez d’autres dépendances qui doivent être initialisées, faites-le ici également._

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.MacOS;
    // also add a using for the Xamarin.Forms project, if the namespace is different to this file
    ...
    [Register("AppDelegate")]
    public class AppDelegate : FormsApplicationDelegate
    {
        NSWindow window;
        public AppDelegate()
        {
            var style = NSWindowStyle.Closable | NSWindowStyle.Resizable | NSWindowStyle.Titled;

            var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);
            window = new NSWindow(rect, style, NSBackingStore.Buffered, false);
            window.Title = "Xamarin.Forms on Mac!"; // choose your own Title here
            window.TitleVisibility = NSWindowTitleVisibility.Hidden;
        }

        public override NSWindow MainWindow
        {
            get { return window; }
        }

        public override void DidFinishLaunching(NSNotification notification)
        {
            Forms.Init();
            LoadApplication(new App());
            base.DidFinishLaunching(notification);
        }
    }
    ```

9. Double-cliquez sur **main. Storyboard** pour modifier dans Xcode. Sélectionnez la **fenêtre** et _désactivez_ la case à cocher **est le contrôleur initial** (cela est dû au fait que le code ci-dessus crée une fenêtre) :

    [![Désactivez la case à cocher est le contrôleur initial dans Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

    Vous pouvez modifier le système de menus dans le Storyboard pour supprimer les éléments indésirables.

10. Enfin, ajoutez des ressources locales (par exemple, fichiers image) à partir des projets de plateforme existants qui sont requis.

11. Le projet Mac doit maintenant exécuter votre Xamarin.Forms code sur MacOS !

## <a name="next-steps"></a>Étapes suivantes

### <a name="styling"></a>Styles

Avec les modifications récentes apportées à `OnPlatform` , vous pouvez désormais cibler un nombre quelconque de plateformes. Qui comprend macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Notez que vous pouvez également doubler sur les plateformes comme suit : `<On Platform="iOS, macOS" ...>` .

### <a name="window-size-and-position"></a>Taille et position de la fenêtre

Vous pouvez ajuster la taille initiale et l’emplacement de la fenêtre dans le `AppDelegate` :

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Problèmes connus

Il s’agit d’une version préliminaire. vous devez donc vous attendre à ce que tous les éléments soient prêts pour la production. Voici quelques opérations que vous pouvez rencontrer lorsque vous ajoutez macOS à vos projets :

### <a name="not-all-nugets-are-ready-for-macos"></a>Les packages NuGet ne sont pas tous prêts pour macOS

Vous constaterez peut-être que certaines des bibliothèques que vous utilisez ne prennent pas encore en charge macOS. Dans ce cas, vous devez envoyer une demande au chargé de maintenance du projet pour l’ajouter. Jusqu’à ce qu’ils prennent en charge, vous devrez peut-être Rechercher d’autres solutions.

### <a name="missing-xamarinforms-features"></a>Fonctionnalités manquantes Xamarin.Forms

Toutes les Xamarin.Forms fonctionnalités ne sont pas exhaustives dans cette version préliminaire. Pour plus d’informations, consultez [prise en charge de la plateforme MacOS Status](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support-macOS-Status) dans le [Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms) référentiel github.

## <a name="related-links"></a>Liens connexes

- [Xamarin.Mac](~/mac/index.yml)
