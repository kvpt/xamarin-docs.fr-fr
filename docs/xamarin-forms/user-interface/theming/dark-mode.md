---
title: Détecter le mode sombre dans les applications Xamarin.Forms
description: Le mode foncé peut être pris en charge dans n’importe quelle application Xamarin.Forms à l’aide d’une combinaison de ResourceDictionaries, DynamicResources et connaissances de la plate-forme.
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 03/13/2020
ms.openlocfilehash: 7fe1a98e6a497a5791f26df2fc96d41781b71ef6
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628310"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>Détecter le mode sombre dans les applications Xamarin.Forms

Les applications Xamarin.Forms peuvent répondre aux changements de thème du système d’exploitation en utilisant la même stratégie qui vous permet de prendre en charge [le thème.](theming.md) La principale différence réside dans la façon dont le changement de thème est déclenché. Le mode foncé désigne un ensemble plus large de préférences d’apparence qui peuvent être définies au niveau du système d’exploitation et auxquelles les applications sont censées respecter et y répondre immédiatement.

Les exigences minimales pour l’utilisation du mode sombre dans vos applications Xamarin.Forms sont les :

- iOS 13 ou plus.
- Android 9 ou plus (Android 9 prend en charge les modes d’apparence que vous pouvez interroger).
- Android 10 ou plus (Android 10 informe les applications de modifications de mode).
- UWP v10.0.10240.0 ou plus.
- Xamarin.Forms (n’importe quelle version).

Le processus de support des modes d’apparence, y compris la lumière et l’obscurité, est le suivant :

1. Définissez les ressources partagées [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)par l’ensemble de votre application dans un .
2. Définissez un thème de dictionnaire de ressources pour chaque mode d’apparence qui fournit des remplacements spécifiques à chaque modèle que vous souhaitez changer.
3. Définissez le thème du mode d’apparence par défaut dans le fichier **App.xaml** de votre application.
4. Stylez votre `DynamicResource` application en utilisant l’extension de balisage où vous voulez que les styles réagissent lorsque les modes d’apparence changent.
5. Ajoutez du code pour écouter les changements de thème OS et chargez le thème correspondant de votre application.

Les captures d’écran suivantes montrent des pages thématiques, pour le mode lumière et sombre :

[![Capture d’écran de la page principale d’une application thématique, sur iOS et Android](theming-images/main-page-both-themes.png "Page principale de l’application thématique")](theming-images/main-page-both-themes-large.png#lightbox "Page principale de l’application thématique")
[![Capture d’écran de la page de détail d’une application thématique, sur iOS et Android](theming-images/detail-page-both-themes.png "Page de détail de l’application thématique")](theming-images/detail-page-both-themes-large.png#lightbox "Page de détail de l’application thématique")

## <a name="define-themes"></a>Définir les thèmes

Suivez le [guide thématique](theming.md) pour les détails étape par étape sur la création de thèmes sombres et légers.

Vous pouvez facilement définir un nouveau thème pour votre application à l’emplacement approprié de votre code de plate-forme. Pour charger un nouveau thème, il suffit de remplacer le dictionnaire de ressources actuel de l’application par un dictionnaire de ressources thématiques de votre choix :

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="detect-and-apply-theme"></a>Détecter et appliquer le thème

La détection du thème en cours [`RequestedTheme`](~/essentials/app-theme.md) d’exécution peut être obtenue en utilisant la fonctionnalité de [Xamarin.Essentials](~/essentials/index.md) (version 1.4.0 ou plus récente). Vous pouvez ensuite créer une méthode d’aide `App` dans une nouvelle classe ou dans la classe pour configurer le thème :

```csharp
public partial class App : Application
{
    public static void ApplyTheme()
    {
        if (AppInfo.RequestedTheme == AppTheme.Dark)
        {
            // Change to dark theme
            // e.g. App.Current.Resources = new YourDarkTheme();
        }
        else
        {
            // Change to light theme
            // e.g. App.Current.Resources = new YourLightTheme();
        }
    }
}
```

## <a name="react-to-appearance-mode-changes"></a>Réagir aux changements de mode d’apparence

Le mode d’apparence d’un appareil peut changer pour une variété de raisons en fonction de la façon dont l’utilisateur a configuré ses préférences, y compris le choix explicite d’un mode, l’heure de la journée ou des facteurs environnementaux tels que la faible luminosité. Vous devrez ajouter du code de plate-forme pour vous assurer que votre application peut réagir à ces modifications, et les sections suivantes en discutent plus en détail.

### <a name="android"></a>Android

Pour prendre en charge le mode sombre de votre application, `Resources/values/styles.xml`vous devez mettre `DayNight` à jour le thème de votre application, qui peut être trouvé dans , pour hériter d’un thème:

```xml
<style name="MainTheme.Base" parent="Theme.AppCompat.DayNight">
```

Si vous avez mis à niveau vers [les composants matériels](https://www.nuget.org/packages/Xamarin.Google.Android.Material/) d’AndroidX (1.1.0-rc2) ou plus récent, vous pouvez utiliser :

```xml
<style name="MainTheme.Base" parent="Theme.MaterialComponents.DayNight">
```

Dans le fichier **MainActivity.cs** de votre application, ajoutez le `ConfigChanges.UiMode` champ à la `ConfigurationChanges` propriété dans l’attribut, `Activity` de sorte que votre application sera notifiée des modifications du mode d’interface utilisateur :

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

Dans le même **fichier MainActivity.cs,** remplacez la `OnConfigurationChanged` méthode :

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);
    App.ApplyTheme();
}
```

### <a name="ios"></a>iOS

Sur iOS, les changements de mode d’apparence sont notifiés sur l’UIViewController, qui dans Xamarin.Forms est le `ContentPage`. Afin de remplacer cette méthode, créez un rendu personnalisé `PageRenderer.cs`dans votre projet iOS appelé :

```csharp
using System;
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(ContentPage), typeof(YourApp.iOS.Renderers.PageRenderer))]
namespace YourApp.iOS.Renderers
{
    public class PageRenderer : Xamarin.Forms.Platform.iOS.PageRenderer
    {
        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                App.ApplyTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            App.ApplyTheme();
        }
    }
}
```

La suppression `TraitCollectionDidChange` de la méthode vous `UserInterfaceStyle` permet d’agir sur un changement.

### <a name="uwp"></a>UWP

Sur UWP, ajoutez le code suivant au fichier **MainPage.xaml.cs** de votre application :

```csharp
public sealed partial class MainPage
{
    UISettings uiSettings;

    public MainPage()
    {
        this.InitializeComponent();

        LoadApplication(new YourApp.App());

        uiSettings = new UISettings();
        uiSettings.ColorValuesChanged += ColorValuesChanged;
    }

    private void ColorValuesChanged(UISettings sender, object args)
    {
        Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
        {
            App.ApplyTheme();
        });
    }
}
```

## <a name="related-links"></a>Liens connexes

- [Thème (échantillon)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styles dynamiques dans Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
