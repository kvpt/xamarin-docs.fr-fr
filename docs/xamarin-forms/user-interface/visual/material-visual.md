---
title: Xamarin.Forms matériau Visual
description: Xamarin.Forms matériau Visual peut servir à créer des applications de Xamarin.Forms recherchent identique, ou en grande partie identique, sur iOS et Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: a626532ac507185b6c01abb5327efa7015c787f5
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512899"
---
# <a name="xamarinforms-material-visual"></a>Xamarin.Forms matériau Visual

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

[Conception matérielle](https://material.io) est un système de conception robuste créé par Google, qui régit la taille, la couleur, l’espacement et autres aspects de la façon dont les vues et dispositions doivent ressemblent et se comportent.

Xamarin.Forms matériau Visual utilisable pour appliquer les règles de conception de matériau aux applications Xamarin.Forms, création d’applications qui recherchent identique, ou en grande partie identique, sur iOS et Android. Lorsque Visual matériel est activé, vues prises en charge adoptent la même conception multiplateforme, création d’une apparence unifiée. Cela est possible avec les convertisseurs de matériau, qui s’appliquent les règles de conception de matériau.

Le processus d’activation Xamarin.Forms matériau Visual dans votre application est :

1. Ajouter le [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) package NuGet pour iOS et projets de plateforme Android. Ce package NuGet fournit des convertisseurs de Material Design optimisés sur iOS et Android. Sur iOS, le package fournit la dépendance transitive à [Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), qui est un C# liaison à Google [composants de matériau pour iOS](https://material.io/develop/ios/). Sur Android, le package fournit des cibles de génération pour vous assurer que votre TargetFramework est correctement configuré.
1. Initialiser les convertisseurs de material dans chaque projet de plateforme. Pour plus d’informations, consultez [initialiser convertisseurs material](#initialize-material-renderers).
1. Consommer les convertisseurs de matériau en définissant le [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriété `Material` sur toutes les pages qui doivent adopter les règles de conception de matériau. Pour plus d’informations, consultez [consommer convertisseurs material](#consume-material-renderers).
1. [facultatif] Personnaliser les convertisseurs de matériau. Pour plus d’informations, consultez [personnaliser les convertisseurs material](#customize-material-renderers).

> [!IMPORTANT]
> Sur Android, les convertisseurs de matériau requièrent une version minimale de 5.0 (API 21) ou supérieur, ainsi qu’un TargetFramework de version 9.0 (28 API). En outre, votre projet de plateforme nécessite les bibliothèques de prise en charge Android 28.0.0 ou version ultérieure, et son thème doit hériter d’un thème de composants de matériels ou de continuer à hériter d’un thème AppCompat. Pour plus d’informations, consultez [mise en route avec les composants de matériau pour Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Convertisseurs de matériau figurent actuellement dans le [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) package NuGet pour les vues suivantes :

- [`Button`](xref:Xamarin.Forms.Button)
- `CheckBox`
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)

Point de vue fonctionnel, les convertisseurs de matériau ne diffèrent pour les convertisseurs de valeur par défaut.

## <a name="initialize-material-renderers"></a>Initialiser des convertisseurs de matériau

Après avoir installé le [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) de package NuGet, le matériel de convertisseurs doivent être initialisés dans chaque projet de plateforme.

Sur iOS, cela doit être effectuée dans **AppDelegate.cs** en appelant le `FormsMaterial.Init` méthode *après* le `Xamarin.Forms.Forms.Init` méthode :

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

Sur Android, cela doit être effectuée dans **MainActivity.cs** en appelant le `FormsMaterial.Init` méthode *après* le `Xamarin.Forms.Forms.Init` méthode :

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>Consommer des convertisseurs de matériau

Les applications peuvent choisissent d’utiliser les convertisseurs de matériau en définissant le [ `VisualElement.Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriété sur une page, la disposition ou la vue, à `Material`:

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

Le code C# équivalent est :

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

Le [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriété peut être définie sur n’importe quel type qui implémente `IVisual`, avec le [ `VisualMarker` ](xref:Xamarin.Forms.VisualMarker) classe fournissant les éléments suivants `IVisual` propriétés :

- `Default` – Indique que la vue doit être restituées à l’aide du convertisseur par défaut.
- `MatchParent` – Indique que la vue doit utiliser le convertisseur de même que son parent direct.
- `Material` – Indique que la vue doit être restituées à l’aide d’un convertisseur de matériau.

> [!IMPORTANT]
> Le [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriété est définie dans le [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) (classe), avec des vues qui héritent le `Visual` valeur de la propriété de leurs parents. Par conséquent, si le `Visual` propriété sur un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) garantit que toutes les vues prises en charge dans la page utilisera ce visuel. En outre, le `Visual` propriété peut être substituée sur une vue.

Les captures d’écran suivantes montrent une interface utilisateur restituée à l’aide des convertisseurs de valeur par défaut :

[![Capture d’écran de convertisseurs de valeur par défaut, sur iOS et Android](material-visual-images/default-renderers.png "vues à l’aide des convertisseurs de valeur par défaut")](material-visual-images/default-renderers-large.png#lightbox)

Les captures d’écran suivantes montrent la même interface utilisateur restituée à l’aide des convertisseurs de matériel :

[![Capture d’écran de convertisseurs de matériau, sur iOS et Android](material-visual-images/material-renderers.png "vues à l’aide de convertisseurs de matériau")](material-visual-images/material-renderers-large.png#lightbox)

Les principales différences visibles entre les convertisseurs de valeur par défaut et les convertisseurs de matériau, illustrées ici, sont les convertisseurs de matériau de majuscule [ `Button` ](xref:Xamarin.Forms.Button) texte et arrondir les angles du [ `Frame` ](xref:Xamarin.Forms.Frame)bordures. Toutefois, matériau convertisseurs utilisent des contrôles natifs, et par conséquent, il peut toujours y différences d’interface utilisateur entre les plateformes pour des domaines tels que les polices, des ombres, des couleurs et une élévation.

> [!NOTE]
> MATÉRIAU conception des composants conformes étroitement aux lignes directrices de Google. Par conséquent, les convertisseurs de Material Design sont orientées vers le dimensionnement et le comportement. Lorsque vous avez besoin de mieux contrôler les styles ou le comportement, vous pouvez toujours créer votre propre [effet](~/xamarin-forms/app-fundamentals/effects/index.md), [comportement](~/xamarin-forms/app-fundamentals/behaviors/index.md), ou [convertisseur personnalisé](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) pour obtenir les détails que vous avez besoin.

## <a name="customize-material-renderers"></a>Personnaliser les convertisseurs de matériau

Convertisseurs Material peuvent éventuellement être personnalisés, tout comme les convertisseurs de valeur par défaut, via les classes de base suivantes :

- `MaterialButtonRenderer`
- `MaterialCheckBoxRenderer`
- `MaterialEntryRenderer`
- `MaterialFrameRenderer`
- `MaterialProgressBarRenderer`
- `MaterialDatePickerRenderer`
- `MaterialTimePickerRenderer`
- `MaterialPickerRenderer`
- `MaterialActivityIndicatorRenderer`
- `MaterialEditorRenderer`
- `MaterialSliderRenderer`
- `MaterialStepperRenderer`

Le code suivant montre un exemple de personnalisation la `MaterialProgressBarRenderer` classe :

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        ...
    }
}
```

Dans cet exemple, le `ExportRendererAttribute` Spécifie que le `CustomMaterialProgressBarRenderer` classe permet de restituer le [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar) vue, avec la `IVisual` type inscrit comme troisième argument.

> [!NOTE]
> Un convertisseur qui spécifie un `IVisual` type, dans le cadre de son `ExportRendererAttribute`, sera utilisé pour restituer participent aux vues, plutôt que le convertisseur par défaut. Au moment de sélection du convertisseur, le `Visual` propriété de la vue est inspectée et incluse dans le processus de sélection de convertisseur.

Pour plus d’informations sur les convertisseurs personnalisés, consultez [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Liens connexes

- [MATÉRIAU Visual (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [Créer un convertisseur de Xamarin.Forms Visual](create.md)
- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
