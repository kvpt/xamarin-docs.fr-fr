---
title: Élément visuel Xamarin. Forms
description: L’élément visuel Xamarin. Forms peut être utilisé pour créer des applications Xamarin. Forms qui semblent quasiment identiques sur iOS et Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/18/2019
ms.openlocfilehash: b447bd255270eb87f6139ddacadb9d31348ab7d7
ms.sourcegitcommit: db2cf1b54125499d5320636927484707810c8e27
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72850494"
---
# <a name="xamarinforms-material-visual"></a>Élément visuel Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

La [conception de matériau](https://material.io) est un système de conception consignes strictes créé par Google, qui régit la taille, la couleur, l’espacement et d’autres aspects liés à l’apparence et au comportement des vues et des dispositions.

L’élément visuel Xamarin. Forms peut être utilisé pour appliquer des règles de conception de matériau aux applications Xamarin. Forms, créant ainsi des applications qui semblent quasiment identiques sur iOS et Android. Lorsque l’élément visuel matériel est activé, les vues prises en charge adoptent la même conception multiplateforme, créant ainsi une apparence unifiée.

[captures d’écran de l’élément visuel![](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

Le processus d’activation de l’élément visuel Xamarin. Forms dans votre application est le suivant :

1. Ajoutez le package NuGet [Xamarin. Forms. Visual. Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) à vos projets de plateforme iOS et Android. Ce package NuGet fournit des convertisseurs de conception de matériau optimisés sur iOS et Android. Sur iOS, le package fournit la dépendance transitive à [Xamarin. iOS. MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), qui est une C# liaison avec les composants de la documentation de Google [pour iOS](https://material.io/develop/ios/). Sur Android, le package fournit des cibles de génération pour s’assurer que la configuration de TargetFramework est correctement configurée.
1. Initialisez le visuel de matériau dans chaque projet de plateforme. Pour plus d’informations, consultez [initialiser un élément visuel](#initialize-material-visual).
1. Créez des contrôles visuels de matériau en affectant à la propriété [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) la valeur `Material` sur les pages qui doivent adopter les règles de conception de matériau. Pour plus d’informations, consultez [utiliser des convertisseurs de matériau](#apply-material-visual).
1. facultatif Personnaliser les contrôles de matériau. Pour plus d’informations, consultez [personnaliser les contrôles de matériau](#customize-material-visual).

> [!IMPORTANT]
> Sur Android, le visuel matériau requiert une version minimale de 5,0 (API 21) ou supérieure, et une valeur de la version 9,0 (API 28). En outre, votre projet de plateforme requiert des bibliothèques de prise en charge Android 28.0.0 ou une version ultérieure, et son thème doit hériter d’un thème de composants de matériau ou continuer d’hériter d’un thème AppCompat. Pour plus d’informations, consultez [prise en main des composants de matériau pour Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Le visuel matériau prend actuellement en charge les contrôles suivants :

- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Button`](xref:Xamarin.Forms.Button)
- `CheckBox`
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

Les contrôles de matériau sont réalisés par les convertisseurs de matériau, qui appliquent les règles de conception de matériau. Fonctionnellement, les convertisseurs de matériaux ne sont pas différents des convertisseurs par défaut. Pour plus d’informations, consultez [personnaliser un élément visuel](#customize-material-visual).

## <a name="initialize-material-visual"></a>Initialiser un élément visuel

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

## <a name="apply-material-visual"></a>Appliquer un élément visuel de matériau

Les applications peuvent activer le visuel matériau en définissant la propriété [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) sur une page, une disposition ou une vue, sur `Material`:

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

L’affectation de la valeur `Material` à la propriété `VisualElement.Visual` indique à votre application d’utiliser les convertisseurs visuels Material à la place des convertisseurs par défaut. La propriété [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) peut être définie sur tout type qui implémente `IVisual`, avec la classe [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) fournissant les propriétés de `IVisual` suivantes :

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

## <a name="customize-material-visual"></a>Personnaliser l’élément visuel de matériau

Le package NuGet de matériau visuel est une collection de convertisseurs qui réalisent les contrôles Xamarin. Forms. La personnalisation des contrôles visuels de matériaux est identique à la personnalisation des contrôles par défaut.

Les effets sont la technique recommandée lorsque l’objectif est de personnaliser un contrôle existant. Si un convertisseur visuel de matériau existe, il est moins pratique de personnaliser le contrôle avec un effet qu’il ne doit sous-traiter le convertisseur. Pour plus d’informations sur les effets, consultez [effets Xamarin. Forms](~/xamarin-forms/app-fundamentals/effects/index.md).

Les convertisseurs personnalisés sont la technique recommandée lorsqu’un convertisseur de matériau n’existe pas. Les classes de convertisseur suivantes sont incluses avec l’élément visuel Material :

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

La sous-classe d’un convertisseur de matériau est presque identique aux convertisseurs non matériels. Toutefois, lors de l’exportation d’un convertisseur qui sous-classe un convertisseur de matériau, vous devez fournir un troisième argument à l’attribut `ExportRenderer` qui spécifie le type de `VisualMarker.MaterialVisual` :

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        //...
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
- [Effets Xamarin. Forms](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
