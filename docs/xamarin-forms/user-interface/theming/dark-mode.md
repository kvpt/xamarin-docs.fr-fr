---
title: Détecter le mode sombre dans les applications Xamarin. Forms
description: Le mode Dark peut être pris en charge dans n’importe quelle application Xamarin. Forms à l’aide d’une combinaison de ResourceDictionaries, DynamicResources et de la plateforme.
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/19/2020
ms.openlocfilehash: 7136e3240a39321b2d67ca29c16a0758cf5c4cfb
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "78292146"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>Détecter le mode sombre dans les applications Xamarin. Forms

Les applications Xamarin. Forms peuvent répondre aux modifications de thème du système d’exploitation à l’aide de la même stratégie qui vous permet de [les](theming.md)prendre en charge. La principale différence réside dans le déclenchement de la modification du thème. Le mode Dark fait référence à un ensemble plus large de préférences d’apparence qui peuvent être définies au niveau du système d’exploitation, et quelles applications doivent respecter et répondre immédiatement à.

La configuration minimale requise pour l’utilisation du mode Dark dans vos applications Xamarin. Forms est la suivante :

- iOS 13 ou version ultérieure.
- Android 9 ou version ultérieure (Android 9 prend en charge les modes d’apparence que vous pouvez interroger).
- Android 10 ou version ultérieure (Android 10 avertit les applications des modifications du mode).
- UWP v 10.0.10240.0 ou version ultérieure.
- Xamarin. Forms (n’importe quelle version).

Le processus de prise en charge des modes d’apparence, y compris clair et sombre, est le suivant :

1. Définissez les ressources partagées par l’ensemble de votre application dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).
2. Définissez un thème de dictionnaire de ressources pour chaque mode d’apparence qui fournit des remplacements spécifiques à chaque style que vous souhaitez modifier.
3. Définissez le thème mode d’apparence par défaut dans le fichier **app. Xaml** de votre application.
4. Attribuez un style à votre application à l’aide de l’extension de balisage `DynamicResource` où vous souhaitez que les styles réagissent lorsque les modes d’apparence changent.
5. Ajoutez du code pour écouter les modifications apportées au thème du système d’exploitation et charger le thème correspondant de votre application.

Les captures d’écran suivantes montrent les pages à thème, en mode Light et Dark :

[![Capture d’écran de la page principale d’une application à thème, sur iOS et android](theming-images/main-page-both-themes.png "Page principale de l’application à thème")](theming-images/main-page-both-themes-large.png#lightbox "Page principale de l’application à thème")
[ ![capture d’écran de la page de détails d’une application à thème, sur iOS et Android](theming-images/detail-page-both-themes.png "Page de détails de l’application à thème")](theming-images/detail-page-both-themes-large.png#lightbox "Page de détails de l’application à thème")

## <a name="define-themes"></a>Définir des thèmes

Suivez le [Guide](theming.md) pour obtenir des détails pas à pas sur la création de thèmes sombres et légers.

## <a name="react-to-appearance-mode-changes"></a>Réagir aux modifications du mode d’apparence

Le mode d’apparence sur un appareil peut changer pour diverses raisons, en fonction de la façon dont l’utilisateur a configuré ses préférences, notamment le choix explicite d’un mode, l’heure de la journée ou des facteurs environnementaux tels que le faible éclairage. Vous devez ajouter du code de plateforme pour vous assurer que votre application peut réagir à ces modifications, et les sections suivantes abordent ce sujet plus en détail.

### <a name="android"></a>Android

Dans le fichier **MainActivity.cs** de votre application, ajoutez le champ `ConfigChanges.UiMode` à la propriété `ConfigurationChanges` dans l’attribut `Activity`, afin que votre application soit informée des modifications du mode interface utilisateur :

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

Dans le même fichier **MainActivity.cs** , remplacez la méthode `OnConfigurationChanged` :

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);

    if ((newConfig.UiMode & UiMode.NightNo) != 0)
    {
        // change to light theme
        // e.g. App.Current.Resources = new YourLightTheme();
    }
    else
    {
        // change to dark theme
        // e.g. App.Current.Resources = new YourDarkTheme();
    }
}
```

### <a name="ios"></a>iOS

Sur iOS, les modifications du mode apparence sont notifiées sur le UIViewController, qui est le `ContentPage`dans Xamarin. Forms. Pour substituer cette méthode, créez un convertisseur personnalisé dans votre projet iOS nommé `PageRenderer.cs`:

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
                SetAppTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            if(this.TraitCollection.UserInterfaceStyle != previousTraitCollection.UserInterfaceStyle)
            {
                SetAppTheme();
            }
        }

        void SetAppTheme()
        {
            if (this.TraitCollection.UserInterfaceStyle == UIUserInterfaceStyle.Dark)
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            }
            else
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            }
        }
    }
}
```

La substitution de la méthode `TraitCollectionDidChange` vous permet d’agir sur une `UserInterfaceStyle` modification.

### <a name="uwp"></a>UWP

Sur UWP, ajoutez le code suivant au fichier **MainPage.Xaml.cs** de votre application :

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
        var backgroundColor = sender.GetColorValue(UIColorType.Background);
        var isDarkMode = backgroundColor == Colors.Black;
        if(isDarkMode)
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            });
        }
        else
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            });
        }
    }
}
```

## <a name="set-dark-and-light-themes"></a>Définir des thèmes sombres et légers

Une fois le Guide de [thèmes](theming.md) , vous pouvez facilement définir un nouveau thème pour votre application à l’emplacement approprié du code de plateforme ci-dessus. Pour charger un nouveau thème, il vous suffit de remplacer le dictionnaire de ressources actuel de l’application par un dictionnaire de ressources à thème de votre choix :

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="related-links"></a>Liens connexes

- [Thèmes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styles dynamiques dans Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
