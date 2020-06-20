---
title: Implémentation d’une vue
description: Cet article explique comment créer un convertisseur personnalisé pour un Xamarin.Forms contrôle personnalisé utilisé pour afficher un flux vidéo de préversion à partir de l’appareil photo de l’appareil.
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c239955a093120c3a16ea3236946eb645ea9a4b4
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84570802"
---
# <a name="implementing-a-view"></a>Implémentation d’une vue

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-view)

_Les contrôles d’interface utilisateur personnalisés Xamarin. Forms doivent dériver de la classe d’affichage, qui est utilisée pour placer des mises en page et des contrôles à l’écran. Cet article explique comment créer un convertisseur personnalisé pour un Xamarin.Forms contrôle personnalisé utilisé pour afficher un flux vidéo de préversion à partir de l’appareil photo de l’appareil._

Chaque Xamarin.Forms vue possède un convertisseur associé pour chaque plateforme qui crée une instance d’un contrôle natif. Lorsqu’un [`View`](xref:Xamarin.Forms.View) est rendu par une Xamarin.Forms application dans iOS, la `ViewRenderer` classe est instanciée, qui à son tour instancie un `UIView` contrôle natif. Sur la plateforme Android, la classe `ViewRenderer` instancie un contrôle `View` natif. Sur la plateforme Windows universelle (UWP), la classe `ViewRenderer` instancie un contrôle `FrameworkElement` natif. Pour plus d’informations sur le convertisseur et les classes de contrôle natifs sur lesquelles les Xamarin.Forms contrôles sont mappés, consultez [classes de base de convertisseur et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la relation entre le [`View`](xref:Xamarin.Forms.View) et les contrôles natifs correspondants qui l’implémentent :

![](view-images/view-classes.png "Relationship Between the View Class and its Implementing Native Classes")

Le processus de rendu peut être utilisé pour implémenter des personnalisations spécifiques à la plateforme en créant un convertisseur personnalisé pour un [`View`](xref:Xamarin.Forms.View) sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créer](#creating-the-custom-control) un Xamarin.Forms contrôle personnalisé.
1. [Utilise](#consuming-the-custom-control) le contrôle personnalisé à partir de Xamarin.Forms .
1. [Créez](#creating-the-custom-renderer-on-each-platform) le renderer personnalisé pour le contrôle sur chaque plateforme.

Chaque élément va maintenant être abordé tour à tour, afin d’implémenter un renderer `CameraPreview` qui affiche un flux vidéo d’aperçu à partir de la caméra de l’appareil. Le fait d’appuyer sur le flux vidéo l’arrête et le démarre.

## <a name="creating-the-custom-control"></a>Création du contrôle personnalisé

Un contrôle personnalisé peut être créé en sous-classant la [`View`](xref:Xamarin.Forms.View) classe, comme indiqué dans l’exemple de code suivant :

```csharp
public class CameraPreview : View
{
  public static readonly BindableProperty CameraProperty = BindableProperty.Create (
    propertyName: "Camera",
    returnType: typeof(CameraOptions),
    declaringType: typeof(CameraPreview),
    defaultValue: CameraOptions.Rear);

  public CameraOptions Camera {
    get { return (CameraOptions)GetValue (CameraProperty); }
    set { SetValue (CameraProperty, value); }
  }
}
```

Le contrôle personnalisé `CameraPreview` est créé dans le projet de bibliothèque .NET Standard et définit l’API pour le contrôle. Le contrôle personnalisé expose une propriété `Camera` qui est utilisée pour contrôler si le flux vidéo doit être affiché à partir de la caméra avant ou arrière de l’appareil. Si aucune valeur n’est spécifiée pour la propriété `Camera` lors de la création du contrôle, la caméra arrière est spécifiée par défaut.

## <a name="consuming-the-custom-control"></a>Consommation du contrôle personnalisé

Vous pouvez référencer le contrôle personnalisé `CameraPreview` en XAML dans le projet de bibliothèque .NET Standard en déclarant un espace de noms pour son emplacement et en utilisant le préfixe d’espace de noms sur l’élément de contrôle personnalisé. L’exemple de code suivant montre comment le contrôle personnalisé `CameraPreview` peut être consommé par une page XAML :

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             ...>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Camera Preview:" />
            <local:CameraPreview Camera="Rear"
              HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Le préfixe d’espace de noms `local` peut porter n’importe quel nom. Toutefois, les valeurs `clr-namespace` et `assembly` doivent correspondre aux détails du contrôle personnalisé. Une fois l’espace de noms déclaré, le préfixe est utilisé pour référencer le contrôle personnalisé.

L’exemple de code suivant montre comment le contrôle personnalisé `CameraPreview` peut être consommé par une page C# :

```csharp
public class MainPageCS : ContentPage
{
  public MainPageCS ()
  {
    ...
    Content = new StackLayout {
      Children = {
        new Label { Text = "Camera Preview:" },
        new CameraPreview {
          Camera = CameraOptions.Rear,
          HorizontalOptions = LayoutOptions.FillAndExpand,
          VerticalOptions = LayoutOptions.FillAndExpand
        }
      }
    };
  }
}
```

Une instance du contrôle personnalisé `CameraPreview` est utilisée pour afficher le flux vidéo d’aperçu à partir de la caméra de l’appareil. Indépendamment de la spécification éventuelle d’une valeur pour la propriété `Camera`, la personnalisation du contrôle est effectuée dans le renderer personnalisé.

Un renderer personnalisé peut maintenant être ajouté à chaque projet d’application pour créer des contrôles d’aperçu de la caméra spécifiques à la plateforme.

## <a name="creating-the-custom-renderer-on-each-platform"></a>Création du renderer personnalisé sur chaque plateforme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la classe `ViewRenderer<T1,T2>` qui restitue le contrôle personnalisé. Le premier argument de type doit être le contrôle personnalisé auquel le renderer est destiné ; dans le cas présent, `CameraPreview`. Le deuxième argument de type doit être le contrôle natif qui implémentera le contrôle personnalisé.
1. Remplacez la méthode `OnElementChanged` qui restitue le contrôle personnalisé et écrivez une logique pour le personnaliser. Cette méthode est appelée lorsque le Xamarin.Forms contrôle correspondant est créé.
1. Ajoutez un `ExportRenderer` attribut à la classe de convertisseur personnalisée pour spécifier qu’il sera utilisé pour restituer le Xamarin.Forms contrôle personnalisé. Cet attribut est utilisé pour inscrire le convertisseur personnalisé avec Xamarin.Forms .

> [!NOTE]
> Pour la plupart des Xamarin.Forms éléments, il est facultatif de fournir un convertisseur personnalisé dans chaque projet de plateforme. Si un renderer personnalisé n’est pas inscrit, le renderer par défaut de la classe de base du contrôle est utilisé. Toutefois, les renderers personnalisés sont nécessaires dans chaque projet de plateforme lors du rendu d’un élément [View](xref:Xamarin.Forms.View).

Le diagramme suivant illustre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](view-images/solution-structure.png "CameraPreview Custom Renderer Project Responsibilities")

Le contrôle personnalisé `CameraPreview` est restitué par des classes de renderer spécifiques à la plateforme qui dérivent toutes de la classe `ViewRenderer` pour chaque plateforme. Il en résulte le rendu de chaque contrôle personnalisé `CameraPreview` avec des contrôles spécifiques à la plateforme, comme le montrent les captures d’écran suivantes :

![](view-images/screenshots.png "CameraPreview on each Platform")

La `ViewRenderer` classe expose la `OnElementChanged` méthode, qui est appelée lorsque le Xamarin.Forms contrôle personnalisé est créé pour restituer le contrôle natif correspondant. Cette méthode prend un paramètre `ElementChangedEventArgs` qui contient les propriétés `OldElement` et `NewElement`. Ces propriétés représentent l' Xamarin.Forms élément auquel le convertisseur *a été* attaché, et l' Xamarin.Forms élément auquel le convertisseur *est* attaché, respectivement. Dans l’exemple d’application, la propriété `OldElement` sera `null` et la propriété `NewElement` contiendra une référence à l’instance `CameraPreview`.

Une version substituée de la méthode `OnElementChanged`, dans chaque classe de renderer spécifique à la plateforme, est l’emplacement où effectuer l’instanciation et la personnalisation du contrôle natif. La méthode `SetNativeControl` doit être utilisée pour instancier le contrôle natif, et cette méthode affecte également la référence de contrôle à la propriété `Control`. En outre, une référence au Xamarin.Forms contrôle rendu peut être obtenue via la `Element` propriété.

Dans certains cas, la méthode `OnElementChanged` peut être appelée plusieurs fois. Ainsi, pour éviter les fuites de mémoire, vous devez être vigilant au moment de l’instanciation d’un nouveau contrôle natif. L’approche à utiliser lors de l’instanciation d’un nouveau contrôle natif dans un renderer personnalisé est indiquée dans l’exemple de code suivant :

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    if (Control == null) {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

Un nouveau contrôle natif doit uniquement être instancié une seule fois, quand la propriété `Control` a la valeur `null`. En outre, le contrôle doit uniquement être créé, configuré et les gestionnaires d’événements auxquels il est abonné lorsque le convertisseur personnalisé est attaché à un nouvel Xamarin.Forms élément. De même, vous devez vous désabonner de tous les gestionnaires d’événements auxquels vous vous êtes abonné uniquement quand l’élément auquel le renderer est attaché change. Le choix de cette approche permet de créer un renderer personnalisé performant qui ne subit pas de fuites de mémoire.

> [!IMPORTANT]
> La méthode `SetNativeControl` doit uniquement être appelée si `e.NewElement` n’est pas `null`.

Chaque classe de convertisseur personnalisé est décorée avec un `ExportRenderer` attribut qui inscrit le convertisseur auprès de Xamarin.Forms . L’attribut accepte deux paramètres : le nom de type du Xamarin.Forms contrôle personnalisé rendu et le nom de type du convertisseur personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

Les sections suivantes décrivent l’implémentation de chaque classe de renderer personnalisé spécifique à la plateforme.

### <a name="creating-the-custom-renderer-on-ios"></a>Création du renderer personnalisé sur iOS

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme iOS :

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, UICameraPreview>
    {
        UICameraPreview uiCameraPreview;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                if (Control == null) {
                  uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                  SetNativeControl (uiCameraPreview);
                }
                // Subscribe
                uiCameraPreview.Tapped += OnCameraPreviewTapped;
            }
        }

        void OnCameraPreviewTapped (object sender, EventArgs e)
        {
            if (uiCameraPreview.IsPreviewing) {
                uiCameraPreview.CaptureSession.StopRunning ();
                uiCameraPreview.IsPreviewing = false;
            } else {
                uiCameraPreview.CaptureSession.StartRunning ();
                uiCameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

Sous réserve que la propriété `Control` ait la valeur `null`, la méthode `SetNativeControl` est appelée pour instancier un nouveau contrôle `UICameraPreview` et lui affecter une référence à la propriété `Control`. Le contrôle `UICameraPreview` est un contrôle personnalisé spécifique à la plateforme qui utilise les API `AVCapture` pour fournir le flux de l’aperçu de la caméra. Il expose un événement `Tapped` qui est géré par la méthode `OnCameraPreviewTapped` pour arrêter et démarrer l’aperçu vidéo quand l’utilisateur appuie dessus. L' `Tapped` événement est abonné à lorsque le convertisseur personnalisé est attaché à un nouvel Xamarin.Forms élément, et n’est pas abonné uniquement lorsque l’élément auquel le convertisseur est attaché est associé aux modifications.

### <a name="creating-the-custom-renderer-on-android"></a>Création du renderer personnalisé sur Android

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme Android :

```csharp
[assembly: ExportRenderer(typeof(CustomRenderer.CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPreviewRenderer : ViewRenderer<CustomRenderer.CameraPreview, CustomRenderer.Droid.CameraPreview>
    {
        CameraPreview cameraPreview;

        public CameraPreviewRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<CustomRenderer.CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                  cameraPreview = new CameraPreview(Context);
                  SetNativeControl(cameraPreview);
                }
                Control.Preview = Camera.Open((int)e.NewElement.Camera);

                // Subscribe
                cameraPreview.Click += OnCameraPreviewClicked;
            }
        }

        void OnCameraPreviewClicked(object sender, EventArgs e)
        {
            if (cameraPreview.IsPreviewing)
            {
                cameraPreview.Preview.StopPreview();
                cameraPreview.IsPreviewing = false;
            }
            else
            {
                cameraPreview.Preview.StartPreview();
                cameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

Sous réserve que la propriété `Control` ait la valeur `null`, la méthode `SetNativeControl` est appelée pour instancier un nouveau contrôle `CameraPreview` et affecter une référence à ce dernier à la propriété `Control`. Le contrôle `CameraPreview` est un contrôle personnalisé spécifique à la plateforme qui utilise l’API `Camera` afin de fournir le flux d’aperçu de la caméra. Le `CameraPreview` contrôle est ensuite configuré, à condition que le convertisseur personnalisé soit attaché à un nouvel Xamarin.Forms élément. Cette configuration implique la création d’un objet `Camera` natif pour accéder à une caméra matérielle particulière et l’inscription d’un gestionnaire d’événements pour traiter l’événement `Click`. À son tour, ce gestionnaire arrête et démarre l’aperçu vidéo quand l’utilisateur appuie dessus. L' `Click` événement est désabonné de si l' Xamarin.Forms élément auquel le convertisseur est attaché change.

### <a name="creating-the-custom-renderer-on-uwp"></a>Création du renderer personnalisé sur UWP

L’exemple de code suivant illustre le renderer personnalisé pour UWP :

```csharp
[assembly: ExportRenderer(typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        ...
        CaptureElement _captureElement;
        bool _isPreviewing;

        protected override void OnElementChanged(ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                  ...
                  _captureElement = new CaptureElement();
                  _captureElement.Stretch = Stretch.UniformToFill;

                  SetupCamera();
                  SetNativeControl(_captureElement);
                }
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped(object sender, TappedRoutedEventArgs e)
        {
            if (_isPreviewing)
            {
                await StopPreviewAsync();
            }
            else
            {
                await StartPreviewAsync();
            }
        }
        ...
    }
}
```

Sous réserve que la propriété `Control` ait la valeur `null`, un nouveau `CaptureElement` est instancié et la méthode `SetupCamera` est appelée, entraînant l’utilisation de l’API `MediaCapture` afin de fournir le flux d’aperçu à partir de la caméra. La méthode `SetNativeControl` est ensuite appelée pour affecter une référence à l’instance `CaptureElement` à la propriété `Control`. Le contrôle `CaptureElement` expose un événement `Tapped` qui est géré par la méthode `OnCameraPreviewTapped` pour arrêter et démarrer l’aperçu vidéo quand l’utilisateur appuie dessus. L' `Tapped` événement est abonné à lorsque le convertisseur personnalisé est attaché à un nouvel Xamarin.Forms élément, et n’est pas abonné uniquement lorsque l’élément auquel le convertisseur est attaché est associé aux modifications.

> [!NOTE]
> Il est important d’arrêter et de supprimer les objets qui fournissent un accès à la caméra dans une application UWP. Si vous ne le faites pas, il peut se produire une interférence avec d’autres applications qui tentent d’accéder à la caméra de l’appareil. Pour plus d’informations, consultez [Afficher l’aperçu de la caméra](/windows/uwp/audio-video-camera/simple-camera-preview-access/).

## <a name="summary"></a>Résumé

Cet article a montré comment créer un convertisseur personnalisé pour un Xamarin.Forms contrôle personnalisé utilisé pour afficher un flux vidéo de préversion à partir de l’appareil photo de l’appareil. Xamarin.Formsles contrôles d’interface utilisateur personnalisés doivent dériver de la [`View`](xref:Xamarin.Forms.View) classe, qui est utilisée pour placer des dispositions et des contrôles à l’écran.

## <a name="related-links"></a>Liens connexes

- [CustomRendererView (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-view)
