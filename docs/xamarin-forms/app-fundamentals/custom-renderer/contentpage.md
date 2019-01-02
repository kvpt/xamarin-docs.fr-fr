---
title: Personnalisation d’un ContentPage
description: Un ContentPage est un élément visuel qui affiche une seule vue et occupe la majeure partie de l’écran. Cet article montre comment créer un renderer personnalisé pour la page ContentPage afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation spécifique à la plateforme.
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: e310726ae4ad72d10e8e2890e9fbf07dda9bc48e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052327"
---
# <a name="customizing-a-contentpage"></a>Personnalisation d’un ContentPage

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)

_Un ContentPage est un élément visuel qui affiche une seule vue et occupe la majeure partie de l’écran. Cet article montre comment créer un renderer personnalisé pour la page ContentPage afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation spécifique à la plateforme._

Chaque contrôle Xamarin.Forms est accompagné d’un renderer pour chaque plateforme qui crée une instance d’un contrôle natif. Quand un [`ContentPage`](xref:Xamarin.Forms.ContentPage) est restitué par une application Xamarin.Forms, dans iOS, la classe `PageRenderer` est instanciée, entraînant à son tour l’instanciation d’un contrôle `UIViewController` natif. Sur la plateforme Android, la classe `PageRenderer` instancie un contrôle `ViewGroup`. Sur la plateforme Windows universelle (UWP), la classe `PageRenderer` instancie un contrôle `FrameworkElement`. Pour plus d’informations sur les classes de renderer et de contrôle natif auxquelles les contrôles Xamarin.Forms sont mappés, consultez [Classes de base de renderer et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la relation entre [`ContentPage`](xref:Xamarin.Forms.ContentPage) et les contrôles natifs correspondants qui l’implémentent :

![](contentpage-images/contentpage-classes.png "Relation entre la classe ContentPage et les contrôles natifs qui l’implémentent")

Vous pouvez tirer profit du processus de rendu pour implémenter des personnalisations spécifiques à la plateforme en créant un renderer personnalisé pour un [`ContentPage`](xref:Xamarin.Forms.ContentPage) sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créez](#Creating_the_Xamarin.Forms_Page) une page Xamarin.Forms.
1. [Consommez](#Consuming_the_Xamarin.Forms_Page) la page à partir de Xamarin.Forms.
1. [Créez](#Creating_the_Page_Renderer_on_each_Platform) le renderer personnalisé pour la page sur chaque plateforme.

Nous allons à présent présenter chaque élément à tour de rôle pour implémenter un `CameraPage` qui fournit un flux vidéo en temps réel et qui permet de capturer une photo.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Création de la page Xamarin.Forms

Vous pouvez ajouter un [`ContentPage`](xref:Xamarin.Forms.ContentPage) inchangé au projet Xamarin.Forms partagé, comme le montre l’exemple de code XAML suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

De même, le fichier code-behind pour le [`ContentPage`](xref:Xamarin.Forms.ContentPage) doit également rester inchangé, comme le montre l’exemple de code suivant :

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

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Consommation de la page Xamarin.Forms

Le `CameraPage` vide doit être affiché par l’application Xamarin.Forms. Cela se produit quand un bouton est actionné dans l’instance `MainPage`, entraînant à son tour l’exécution de la méthode `OnTakePhotoButtonClicked`, comme le montre l’exemple de code suivant :

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Ce code accède simplement à `CameraPage`, sur lequel les renderers personnalisés personnalisent l’apparence de la page sur chaque plateforme.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Création du renderer de page sur chaque plateforme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la classe `PageRenderer`.
1. Remplacez la méthode `OnElementChanged` qui restitue la page native et écrivez la logique pour la personnaliser. La méthode `OnElementChanged` est appelée au moment de la création du contrôle Xamarin.Forms correspondant.
1. Ajoutez un attribut `ExportRenderer` à la classe du renderer de page pour spécifier qu’il sera utilisé pour restituer la page Xamarin.Forms. Cet attribut est utilisé pour inscrire le renderer personnalisé auprès de Xamarin.Forms.

> [!NOTE]
> Il est facultatif de fournir un renderer de page dans chaque projet de plateforme. Si un renderer de page n’est pas inscrit, le renderer par défaut de la page est utilisé.

Le diagramme suivant montre les responsabilités de chaque projet dans l’exemple d’application ainsi que la relation qu’ils entretiennent les uns avec les autres :

![](contentpage-images/solution-structure.png "Responsabilités du projet de renderer personnalisé CameraPage")

L’instance `CameraPage` est restituée par des classes `CameraPageRenderer` spécifiques à la plateforme qui dérivent toutes de la classe `PageRenderer` pour cette plateforme. Chaque instance `CameraPage` est alors restituée avec un flux vidéo en temps réel, comme le montrent les captures d’écran suivantes :

![](contentpage-images/screenshots.png "CameraPage sur chaque plateforme")

La classe `PageRenderer` expose la méthode `OnElementChanged`, qui est appelée quand la page Xamarin.Forms est créée pour restituer le contrôle natif correspondant. Cette méthode prend un paramètre `ElementChangedEventArgs` qui contient les propriétés `OldElement` et `NewElement`. Ces propriétés représentent respectivement l’élément Xamarin.Forms auquel le renderer *était* attaché et l’élément Xamarin.Forms auquel le renderer *est* attaché. Dans l’exemple d’application, la propriété `OldElement` est `null` et la propriété `NewElement` contient une référence à l’instance `CameraPage`.

Une version substituée de la méthode `OnElementChanged` dans la classe `CameraPageRenderer` est l’emplacement où effectuer la personnalisation de la page native. Une référence à l’instance de page Xamarin.Forms en cours de restitution peut être obtenue par le biais de la propriété `Element`.

Chaque classe de renderer personnalisé est décorée avec un attribut `ExportRenderer` qui inscrit le renderer auprès de Xamarin.Forms. L’attribut accepte deux paramètres : le nom de type de la page Xamarin.Forms en cours de restitution et le nom de type du renderer personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

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

L’appel à la méthode `OnElementChanged` de la classe de base instancie un contrôle `UIViewController` iOS. Le flux vidéo en temps réel est restitué à condition que le renderer ne soit pas déjà attaché à un élément Xamarin.Forms existant et sous réserve qu’une instance de page soit en cours de restitution par le renderer personnalisé.

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

L’appel à la méthode `OnElementChanged` de la classe de base instancie un contrôle `ViewGroup` Android, qui est en fait un groupe de vues. Le flux vidéo en temps réel est restitué à condition que le renderer ne soit pas déjà attaché à un élément Xamarin.Forms existant et sous réserve qu’une instance de page soit en cours de restitution par le renderer personnalisé.

La page est ensuite personnalisée en appelant une série de méthodes qui utilisent l’API `Camera` pour fournir le flux en temps réel de la caméra et la possibilité de capturer une photo, puis la méthode `AddView` est appelée pour ajouter l’interface utilisateur du flux vidéo en temps réel à `ViewGroup`. Sur Android, notez qu’il est également nécessaire de remplacer la méthode `OnLayout` pour effectuer les opérations de mesure et de disposition sur la vue. Pour plus d’informations, consultez l’[exemple de renderer ContentPage](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/).

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

L’appel à la méthode `OnElementChanged` de la classe de base instancie un contrôle `FrameworkElement` sur lequel la page est restituée. Le flux vidéo en temps réel est restitué à condition que le renderer ne soit pas déjà attaché à un élément Xamarin.Forms existant et sous réserve qu’une instance de page soit en cours de restitution par le renderer personnalisé. La page est ensuite personnalisée en appelant une série de méthodes qui utilisent l’API `MediaCapture` pour fournir le flux en temps réel de la caméra et la possibilité de capturer une photo, puis la page personnalisée est ajoutée à la collection `Children` pour affichage.

Quand vous implémentez un renderer personnalisé qui dérive de `PageRenderer` sur UWP, la méthode `ArrangeOverride` doit également être implémentée pour organiser les contrôles de page car le renderer de base ne sait pas quoi en faire. Sinon, vous obtenez une page vierge. Ainsi, dans cet exemple, la méthode `ArrangeOverride` appelle la méthode `Arrange` sur l’instance `Page`.

> [!NOTE]
> Il est important d’arrêter et de supprimer les objets qui fournissent un accès à la caméra dans une application UWP. Si vous ne le faites pas, il peut se produire une interférence avec d’autres applications qui tentent d’accéder à la caméra de l’appareil. Pour plus d’informations, consultez [Afficher l’aperçu de la caméra](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access).

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons vu comment créer un renderer personnalisé pour la page [`ContentPage`](xref:Xamarin.Forms.ContentPage) afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation spécifique à la plateforme. Un `ContentPage` est un élément visuel qui affiche une seule vue et occupe la majeure partie de l’écran.


## <a name="related-links"></a>Liens associés

- [CustomRendererContentPage (exemple)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)
