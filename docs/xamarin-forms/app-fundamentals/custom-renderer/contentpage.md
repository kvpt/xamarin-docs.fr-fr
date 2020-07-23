---
title: Personnalisation d’un ContentPage
description: Un ContentPage est un élément visuel qui affiche une seule vue et occupe la majeure partie de l’écran. Cet article montre comment créer un renderer personnalisé pour la page ContentPage afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation spécifique à la plateforme.
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: adbceca38fca2cbe65c739f0d7fe26f18bdf5450
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939852"
---
# <a name="customizing-a-contentpage"></a>Personnalisation d’un ContentPage

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage)

_Un ContentPage est un élément visuel qui affiche une vue unique et occupe la majeure partie de l’écran. Cet article montre comment créer un convertisseur personnalisé pour la page ContentPage, ce qui permet aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation propre à la plateforme._

Chaque Xamarin.Forms contrôle a un convertisseur associé pour chaque plateforme qui crée une instance d’un contrôle natif. Lorsqu’un [`ContentPage`](xref:Xamarin.Forms.ContentPage) est rendu par une Xamarin.Forms application, dans iOS, la `PageRenderer` classe est instanciée, qui à son tour instancie un `UIViewController` contrôle natif. Sur la plateforme Android, la classe `PageRenderer` instancie un contrôle `ViewGroup`. Sur la plateforme Windows universelle (UWP), la classe `PageRenderer` instancie un contrôle `FrameworkElement`. Pour plus d’informations sur le convertisseur et les classes de contrôle natifs sur lesquelles les Xamarin.Forms contrôles sont mappés, consultez [classes de base de convertisseur et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la relation entre le [`ContentPage`](xref:Xamarin.Forms.ContentPage) et les contrôles natifs correspondants qui l’implémentent :

![Relation entre la classe ContentPage et les contrôles natifs qui l’implémentent](contentpage-images/contentpage-classes.png)

Le processus de rendu peut être utilisé pour implémenter des personnalisations spécifiques à la plateforme en créant un convertisseur personnalisé pour un [`ContentPage`](xref:Xamarin.Forms.ContentPage) sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créer](#creating-the-xamarinforms-page) une Xamarin.Forms page.
1. [Utiliser](#consuming-the-xamarinforms-page) la page à partir de Xamarin.Forms .
1. [Créez](#creating-the-page-renderer-on-each-platform) le renderer personnalisé pour la page sur chaque plateforme.

Nous allons à présent présenter chaque élément à tour de rôle pour implémenter un `CameraPage` qui fournit un flux vidéo en temps réel et qui permet de capturer une photo.

## <a name="creating-the-xamarinforms-page"></a>Création de la Xamarin.Forms page

Un non modifié [`ContentPage`](xref:Xamarin.Forms.ContentPage) peut être ajouté au Xamarin.Forms projet partagé, comme indiqué dans l’exemple de code XAML suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

De même, le fichier code-behind pour le [`ContentPage`](xref:Xamarin.Forms.ContentPage) doit également rester non modifié, comme illustré dans l’exemple de code suivant :

```csharp
public partial class CameraPage : ContentPage
{
    public CameraPage ()
    {
        // A custom renderer is used to display the camera UI
        InitializeComponent ();
    }
}
```

L’exemple de code suivant montre comment créer la page en C# :

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

Une instance de `CameraPage` est utilisée pour afficher le flux vidéo en temps réel sur chaque plateforme. La personnalisation du contrôle étant effectuée dans le renderer personnalisé, aucune implémentation supplémentaire n’est nécessaire dans la classe `CameraPage`.

## <a name="consuming-the-xamarinforms-page"></a>Utilisation de la Xamarin.Forms page

La vide `CameraPage` doit être affichée par l' Xamarin.Forms application. Cela se produit quand un bouton est actionné dans l’instance `MainPage`, entraînant à son tour l’exécution de la méthode `OnTakePhotoButtonClicked`, comme le montre l’exemple de code suivant :

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Ce code accède simplement à `CameraPage`, sur lequel les renderers personnalisés personnalisent l’apparence de la page sur chaque plateforme.

## <a name="creating-the-page-renderer-on-each-platform"></a>Création du renderer de page sur chaque plateforme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la classe `PageRenderer`.
1. Remplacez la méthode `OnElementChanged` qui restitue la page native et écrivez la logique pour la personnaliser. La `OnElementChanged` méthode est appelée lorsque le Xamarin.Forms contrôle correspondant est créé.
1. Ajoutez un `ExportRenderer` attribut à la classe de convertisseur de page pour spécifier qu’elle sera utilisée pour restituer la Xamarin.Forms page. Cet attribut est utilisé pour inscrire le convertisseur personnalisé avec Xamarin.Forms .

> [!NOTE]
> Il est facultatif de fournir un renderer de page dans chaque projet de plateforme. Si un renderer de page n’est pas inscrit, le renderer par défaut de la page est utilisé.

Le diagramme suivant montre les responsabilités de chaque projet dans l’exemple d’application ainsi que la relation qu’ils entretiennent les uns avec les autres :

![Responsabilités du projet de renderer personnalisé CameraPage](contentpage-images/solution-structure.png)

L’instance `CameraPage` est restituée par des classes `CameraPageRenderer` spécifiques à la plateforme qui dérivent toutes de la classe `PageRenderer` pour cette plateforme. Chaque instance `CameraPage` est alors restituée avec un flux vidéo en temps réel, comme le montrent les captures d’écran suivantes :

![CameraPage sur chaque plateforme](contentpage-images/screenshots.png)

La `PageRenderer` classe expose la `OnElementChanged` méthode, qui est appelée lorsque la Xamarin.Forms page est créée pour restituer le contrôle natif correspondant. Cette méthode prend un paramètre `ElementChangedEventArgs` qui contient les propriétés `OldElement` et `NewElement`. Ces propriétés représentent l' Xamarin.Forms élément auquel le convertisseur *a été* attaché, et l' Xamarin.Forms élément auquel le convertisseur *est* attaché, respectivement. Dans l’exemple d’application, la propriété `OldElement` sera `null` et la propriété `NewElement` contiendra une référence à l’instance `CameraPage`.

Une version substituée de la méthode `OnElementChanged` dans la classe `CameraPageRenderer` est l’emplacement où effectuer la personnalisation de la page native. Une référence à l' Xamarin.Forms instance de page en cours de rendu peut être obtenue via la `Element` propriété.

Chaque classe de convertisseur personnalisé est décorée avec un `ExportRenderer` attribut qui inscrit le convertisseur auprès de Xamarin.Forms . L’attribut accepte deux paramètres : le nom de type de la Xamarin.Forms page rendue et le nom de type du convertisseur personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

Les sections suivantes décrivent l’implémentation du renderer personnalisé `CameraPageRenderer` pour chaque plateforme.

### <a name="creating-the-page-renderer-on-ios"></a>Création du renderer de page sur iOS

L’exemple de code suivant illustre le renderer de page pour la plateforme iOS :

```csharp
[assembly:ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                AuthorizeCameraUse ();
            } catch (Exception ex) {
                System.Diagnostics.Debug.WriteLine (@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

L’appel à la méthode `OnElementChanged` de la classe de base instancie un contrôle `UIViewController` iOS. Le flux de la caméra dynamique est rendu uniquement si le convertisseur n’est pas déjà attaché à un Xamarin.Forms élément existant, et à condition qu’une instance de page soit rendue par le convertisseur personnalisé.

La page est ensuite personnalisée par une série de méthodes qui utilisent les API `AVCapture` pour fournir le flux en temps réel à partir de la caméra et la possibilité de capturer une photo.

### <a name="creating-the-page-renderer-on-android"></a>Création du renderer de page sur Android

L’exemple de code suivant illustre le renderer de page pour la plateforme Android :

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
    {
        ...
        public CameraPageRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetupUserInterface();
                SetupEventHandlers();
                AddView(view);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

L’appel à la méthode `OnElementChanged` de la classe de base instancie un contrôle `ViewGroup` Android, qui est en fait un groupe de vues. Le flux de la caméra dynamique est rendu uniquement si le convertisseur n’est pas déjà attaché à un Xamarin.Forms élément existant, et à condition qu’une instance de page soit rendue par le convertisseur personnalisé.

La page est ensuite personnalisée en appelant une série de méthodes qui utilisent l’API `Camera` pour fournir le flux en temps réel de la caméra et la possibilité de capturer une photo, puis la méthode `AddView` est appelée pour ajouter l’interface utilisateur du flux vidéo en temps réel à `ViewGroup`. Sur Android, notez qu’il est également nécessaire de remplacer la méthode `OnLayout` pour effectuer les opérations de mesure et de disposition sur la vue. Pour plus d’informations, consultez l’[exemple de renderer ContentPage](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage).

### <a name="creating-the-page-renderer-on-uwp"></a>Création du renderer de page sur UWP

L’exemple de code suivant illustre le renderer de page pour UWP :

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPageRenderer : PageRenderer
    {
        ...
        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                ...
                SetupUserInterface();
                SetupBasedOnStateAsync();

                this.Children.Add(page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}

```

L’appel à la méthode `OnElementChanged` de la classe de base instancie un contrôle `FrameworkElement` sur lequel la page est restituée. Le flux de la caméra dynamique est rendu uniquement si le convertisseur n’est pas déjà attaché à un Xamarin.Forms élément existant, et à condition qu’une instance de page soit rendue par le convertisseur personnalisé. La page est ensuite personnalisée en appelant une série de méthodes qui utilisent l’API `MediaCapture` pour fournir le flux en temps réel de la caméra et la possibilité de capturer une photo, puis la page personnalisée est ajoutée à la collection `Children` pour affichage.

Quand vous implémentez un renderer personnalisé qui dérive de `PageRenderer` sur UWP, la méthode `ArrangeOverride` doit également être implémentée pour organiser les contrôles de page car le renderer de base ne sait pas quoi en faire. Sinon, vous obtenez une page vierge. Ainsi, dans cet exemple, la méthode `ArrangeOverride` appelle la méthode `Arrange` sur l’instance `Page`.

> [!NOTE]
> Il est important d’arrêter et de supprimer les objets qui fournissent un accès à la caméra dans une application UWP. Si vous ne le faites pas, il peut se produire une interférence avec d’autres applications qui tentent d’accéder à la caméra de l’appareil. Pour plus d’informations, consultez [Afficher l’aperçu de la caméra](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access).

## <a name="summary"></a>Résumé

Cet article a montré comment créer un convertisseur personnalisé pour la [`ContentPage`](xref:Xamarin.Forms.ContentPage) page, ce qui permet aux développeurs de substituer le rendu natif par défaut avec leur propre personnalisation propre à la plateforme. Un `ContentPage` est un élément visuel qui affiche une seule vue et occupe la majeure partie de l’écran.

## <a name="related-links"></a>Liens associés

- [CustomRendererContentPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage)
