---
title: Élément visuel Xamarin. Forms
description: L’élément visuel Xamarin. Forms peut être utilisé pour créer des applications Xamarin. Forms qui semblent identiques, ou en grande partie, sur iOS et Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: b735541d51321231775b025745e68c54552697d3
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "71198495"
---
# <a name="xamarinforms-material-visual"></a>Élément visuel Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

La [conception de matériau](https://material.io) est un système de conception consignes strictes créé par Google, qui régit la taille, la couleur, l’espacement et d’autres aspects liés à l’apparence et au comportement des vues et des dispositions.

L’élément visuel Xamarin. Forms peut être utilisé pour appliquer des règles de conception de matériau aux applications Xamarin. Forms, créant ainsi des applications identiques ou largement identiques, sur iOS et Android. Lorsque l’élément visuel matériel est activé, les vues prises en charge adoptent la même conception multiplateforme, créant ainsi une apparence unifiée. Cela est possible avec les convertisseurs de matériau, qui appliquent les règles de conception de matériau.

Le processus d’activation de l’élément visuel Xamarin. Forms dans votre application est le suivant :

1. Ajoutez le package NuGet [Xamarin. Forms. Visual. Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) à vos projets de plateforme iOS et Android. Ce package NuGet fournit des convertisseurs de conception de matériau optimisés sur iOS et Android. Sur iOS, le package fournit la dépendance transitive à [Xamarin. iOS. MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), qui est une C# liaison avec les composants de la documentation de Google [pour iOS](https://material.io/develop/ios/). Sur Android, le package fournit des cibles de génération pour s’assurer que la configuration de TargetFramework est correctement configurée.
1. Initialisez les convertisseurs de matériau dans chaque projet de plateforme. Pour plus d’informations, consultez [initialiser les convertisseurs de matériau](#initialize-material-renderers).
1. Consommez les convertisseurs de matériau en affectant à la propriété [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) la valeur `Material` sur les pages qui doivent adopter les règles de conception de matériau. Pour plus d’informations, consultez [utiliser des convertisseurs de matériau](#consume-material-renderers).
1. facultatif Personnaliser les convertisseurs de matériau. Pour plus d’informations, consultez [personnaliser les convertisseurs de matériau](#customize-material-renderers).

> [!IMPORTANT]
> Sur Android, les convertisseurs de matériau requièrent une version minimale de 5,0 (API 21) ou supérieure, et un TargetFramework de version 9,0 (API 28). En outre, votre projet de plateforme requiert des bibliothèques de prise en charge Android 28.0.0 ou une version ultérieure, et son thème doit hériter d’un thème de composants de matériau ou continuer d’hériter d’un thème AppCompat. Pour plus d’informations, consultez [prise en main des composants de matériau pour Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Les convertisseurs de matériaux sont actuellement inclus dans le package NuGet [Xamarin. Forms. Visual. Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) pour les vues suivantes :

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

Fonctionnellement, les convertisseurs de matériau ne sont pas différents des convertisseurs par défaut.

## <a name="initialize-material-renderers"></a>Initialiser les convertisseurs de matériau

Après l’installation du package NuGet [Xamarin. Forms. Visual. Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) , les convertisseurs de matériau doivent être initialisés dans chaque projet de plateforme.

Sur iOS, cela doit se produire dans **AppDelegate.cs** en appelant la méthode `Xamarin.Forms.FormsMaterial.Init` *après* la méthode `Xamarin.Forms.Forms.Init` :

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

Sur Android, cela doit se produire dans **MainActivity.cs** en appelant la méthode `Xamarin.Forms.FormsMaterial.Init` *après* la méthode `Xamarin.Forms.Forms.Init` :

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>Utiliser des convertisseurs de matériaux

Les applications peuvent choisir d’utiliser les convertisseurs de matériau en définissant la propriété [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) sur une page, une disposition ou une vue, pour `Material` :

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

La propriété [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) peut être définie sur tout type qui implémente `IVisual`, avec la classe [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) fournissant les propriétés de `IVisual` suivantes :

- `Default` : indique que la vue doit être restituée à l’aide du convertisseur par défaut.
- `MatchParent` : indique que la vue doit utiliser le même convertisseur que son parent direct.
- `Material` : indique que la vue doit être rendue à l’aide d’un convertisseur de matériau.

> [!IMPORTANT]
> La propriété [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) est définie dans la classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , les vues héritant de la valeur de propriété `Visual` de leurs parents. Par conséquent, la définition de la propriété `Visual` sur un [`ContentPage`](xref:Xamarin.Forms.ContentPage) garantit que toutes les vues prises en charge dans la page utiliseront cet visuel. En outre, la propriété `Visual` peut être substituée sur une vue.

Les captures d’écran suivantes montrent une interface utilisateur rendue à l’aide des convertisseurs par défaut :

[![Capture d’écran des convertisseurs par défaut, sur iOS et Android](material-visual-images/default-renderers.png "Vues utilisant les convertisseurs par défaut")](material-visual-images/default-renderers-large.png#lightbox)

Les captures d’écran suivantes montrent la même interface utilisateur rendue à l’aide des convertisseurs de matériau :

[![Capture d’écran des convertisseurs de matériau, sur iOS et Android](material-visual-images/material-renderers.png "Vues utilisant des convertisseurs de matériau")](material-visual-images/material-renderers-large.png#lightbox)

Les principales différences visibles entre les convertisseurs par défaut et les convertisseurs de matériaux, présentées ici, sont que les convertisseurs de matériau mettent en majuscule [`Button`](xref:Xamarin.Forms.Button) texte et arrondissent les angles des bordures de [`Frame`](xref:Xamarin.Forms.Frame) . Toutefois, les convertisseurs de matériau utilisent des contrôles natifs, et par conséquent, il peut subsister des différences d’interface utilisateur entre les plateformes pour des zones telles que les polices, les ombres, les couleurs et l’élévation.

> [!NOTE]
> Les composants de conception de matériau adhèrent étroitement aux recommandations de Google. Par conséquent, les convertisseurs de conception de matériau sont influencés par le dimensionnement et le comportement. Lorsque vous avez besoin d’un meilleur contrôle des styles ou du comportement, vous pouvez toujours créer votre propre [effet](~/xamarin-forms/app-fundamentals/effects/index.md), [comportement](~/xamarin-forms/app-fundamentals/behaviors/index.md)ou [convertisseur personnalisé](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) pour obtenir les détails dont vous avez besoin.

## <a name="customize-material-renderers"></a>Personnaliser les convertisseurs de matériau

Les convertisseurs de matériau peuvent éventuellement être personnalisés, comme les convertisseurs par défaut, via les classes de base suivantes :

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

Le code suivant montre un exemple de personnalisation de la classe `MaterialProgressBarRenderer` :

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

Dans cet exemple, le `ExportRendererAttribute` spécifie que la classe `CustomMaterialProgressBarRenderer` sera utilisée pour restituer la vue [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) , avec le type `IVisual` inscrit comme troisième argument.

> [!NOTE]
> Un convertisseur qui spécifie un type de `IVisual`, dans le cadre de son `ExportRendererAttribute`, sera utilisé pour le rendu de l’option choisi dans les vues, plutôt que dans le convertisseur par défaut. Au moment de la sélection du convertisseur, la propriété `Visual` de la vue est inspectée et incluse dans le processus de sélection du convertisseur.

Pour plus d’informations sur les convertisseurs personnalisés, consultez [convertisseurs personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Liens connexes

- [Élément visuel (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Créer un convertisseur visuel Xamarin. Forms](create.md)
- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
