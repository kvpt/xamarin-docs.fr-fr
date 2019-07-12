---
title: UrhoSharp Windows Support
description: Ce document aborde la prise en charge de Windows de UrhoSharp. Il décrit comment créer un projet, configurer et lancer Urho, intégrer avec WPF et s’intègrent avec UWP.
ms.prod: xamarin
ms.assetid: A4F36014-AE4E-4F07-A1AC-F264AAA68ACF
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 46a028da577a4c49e18cccb681351d7614bb196b
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832641"
---
# <a name="urhosharp-windows-support"></a>Prise en charge de Windows de UrhoSharp

Bien que Urho est une bibliothèque de classes portable, et permet à la même API pour être utilisé sur la plateforme différents pour votre logique de jeu, vous devez toujours initialiser Urho dans votre pilote de plateforme spécifique et dans certains cas, vous pouvez tirer parti des fonctionnalités spécifiques de plateforme .

Dans les pages ci-dessous, supposons que `MyGame` est une sous-classe de la `Application` classe.

**Architectures prises en charge :** uniquement 64 bits de Windows.

Vous pouvez voir des exemples complets montrant comment l’utiliser dans notre [exemples](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples)

## <a name="standalone-project"></a>Projet autonome

### <a name="creating-a-project"></a>Création d'un projet

Créez un projet Console, référencer le Urho NuGet et assurez-vous que vous pouvez localiser les ressources (les répertoires contenant le répertoire de données).

### <a name="configuring-and-launching-urho"></a>Configurant et en lançant Urho

Pour lancer votre application, procédez comme suit :

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

### <a name="example"></a>Exemple

[Exemple complet](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Desktop)

## <a name="integrated-with-wpf"></a>Intégré avec WPF

### <a name="creating-a-project"></a>Création d'un projet

Créer un projet WPF, référencer le Urho NuGet et assurez-vous que vous pouvez localiser les ressources (les répertoires contenant le répertoire de données).

### <a name="configuring-and-launching-urho-from-wpf"></a>Configurant et en lançant Urho de WPF

Créer une sous-classe de `Window` et configurer vos ressources comme suit :

```csharp
    public partial class MainWindow : Window
    {
        Application currentApplication;

        public MainWindow()
        {
            InitializeComponent();
            DesktopUrhoInitializer.AssetsDirectory = @"../../Assets";
            Loaded += (s,e) => RunGame (new MyGame ());
        }

        async void RunGame(MyGame game)
        {
            var urhoSurface = new Panel { Dock = DockStyle.Fill };
            WindowsFormsHost.Child = urhoSurface;
            WindowsFormsHost.Focus();
            urhoSurface.Focus();
            await Task.Yield();
            var appOptions = new ApplicationOptions(assetsFolder: "Data")
                {
                    ExternalWindow = RunInSdlWindow.IsChecked.Value ? IntPtr.Zero : urhoSurface.Handle,
                    LimitFps = false, //true means "limit to 200fps"
                };
            currentApplication = Urho.Application.CreateInstance(value.Type, appOptions);
            currentApplication.Run();
        }
    }
```

### <a name="example"></a>Exemple

[Exemple complet](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/WPF)

## <a name="integrated-with-uwp"></a>Intégré avec UWP

### <a name="creating-a-project"></a>Création d'un projet

Créez un projet UWP, référencer le Urho NuGet et assurez-vous que vous pouvez localiser les ressources (les répertoires contenant le répertoire de données).

### <a name="configuring-and-launching-urho-from-uwp"></a>Configurant et en lançant Urho à partir de UWP

Créer une sous-classe de `Window` et configurer vos ressources comme suit :

```csharp
    {
        InitializeComponent();
        GameTypes = typeof(Sample).GetTypeInfo().Assembly.GetTypes()
            .Where(t => t.GetTypeInfo().IsSubclassOf(typeof(Application)) && t != typeof(Sample))
            .Select((t, i) => new TypeInfo(t, $"{i + 1}. {t.Name}", ""))
            .ToArray();
        DataContext = this;
        Loaded += (s, e) => RunGame (new MyGame ());
    }

    public void RunGame(TypeInfo value)
    {
        //at this moment, UWP supports assets only in pak files (see PackageTool)
        currentApplication = UrhoSurface.Run(value.Type, "Data.pak");
    }
}
```

### <a name="example"></a>Exemples

[Exemple complet](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/UWP)

## <a name="integrated-with-windows-forms"></a>Intégré à Windows Forms

### <a name="creating-a-project"></a>Création d'un projet

Créez un projet Windows Forms, référencer le Urho NuGet et assurez-vous que vous pouvez localiser les ressources (les répertoires contenant le répertoire de données).

### <a name="configuring-and-launching-urho-from-windowsforms"></a>Configurant et en lançant Urho à partir de Windows.Forms

Lancer Urho à partir de votre formulaire, consultez [exemple complet](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/WinForms/SamplesForm.cs)
