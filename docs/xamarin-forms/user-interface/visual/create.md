---
title: Créer un convertisseur visuel Xamarin. Forms
description: Créer des éléments visuels Xamarin. Forms à appliquer de manière sélective aux objets VisualElement, sans avoir à reclasser les vues Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: 8173ebcc174df6e34a53f226a43083bd28941031
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68869375"
---
# <a name="create-a-xamarinforms-visual-renderer"></a>Créer un convertisseur visuel Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

Xamarin. Forms Visual permet de créer des convertisseurs et de les appliquer [`VisualElement`](xref:Xamarin.Forms.VisualElement) de manière sélective aux objets, sans avoir à sous-créer des vues Xamarin. Forms. Un convertisseur qui spécifie un `IVisual` type, dans le cadre de `ExportRendererAttribute`son, sera utilisé pour le rendu de l’option choisi dans les vues, plutôt que dans le convertisseur par défaut. Au moment de sélection du convertisseur, le `Visual` propriété de la vue est inspectée et incluse dans le processus de sélection de convertisseur.

> [!IMPORTANT]
> Actuellement, [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) la propriété ne peut pas être modifiée une fois que la vue a été rendue, mais cela sera modifié dans une version ultérieure.

Le processus de création et d’utilisation d’un convertisseur visuel Xamarin. Forms est le suivant:

1. Créez des convertisseurs de plateforme pour la vue requise. Pour plus d’informations, consultez [créer](#create-platform-renderers)des convertisseurs.
1. Créez un type qui dérive de `IVisual`. Pour plus d’informations, consultez [créer un type IVisual](#create-an-ivisual-type).
1. Inscrivez le `IVisual` type dans le `ExportRendererAttribute` cadre du qui décore les convertisseurs. Pour plus d’informations, consultez [enregistrer le type IVisual](#register-the-ivisual-type).
1. Consommez le convertisseur visuel en affectant le [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) `IVisual` nom à la propriété sur la vue. Pour plus d’informations, consultez [consommer le convertisseur visuel](#consume-the-visual-renderer).
1. facultatif Enregistrez un nom pour le `IVisual` type. Pour plus d’informations, consultez [enregistrer un nom pour le type IVisual](#register-a-name-for-the-ivisual-type).

## <a name="create-platform-renderers"></a>Créer des convertisseurs de plateforme

Pour plus d’informations sur la création d’une classe de convertisseur, consultez [convertisseurs personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Toutefois, Notez qu’un convertisseur visuel Xamarin. Forms est appliqué à une vue sans avoir à sous-classer la vue.

Les classes de convertisseur décrites ici implémentent un [`Button`](xref:Xamarin.Forms.Button) personnalisé qui affiche son texte avec une ombre.

### <a name="ios"></a>iOS

L’exemple de code suivant montre le convertisseur de bouton pour iOS:

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.TitleShadowOffset = new CoreGraphics.CGSize(1, 1);
            Control.SetTitleShadowColor(Color.Black.ToUIColor(), UIKit.UIControlState.Normal);
        }
    }
}
```

### <a name="android"></a>Android

L’exemple de code suivant montre le convertisseur de bouton pour Android:

```csharp
public class CustomButtonRenderer : Xamarin.Forms.Platform.Android.AppCompat.ButtonRenderer
{
    public CustomButtonRenderer(Context context) : base(context)
    {
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.SetShadowLayer(5, 3, 3, Color.Black.ToAndroid());
        }
    }
}
```

## <a name="create-an-ivisual-type"></a>Créer un type IVisual

Dans votre bibliothèque multiplateforme, créez un type qui dérive de `IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

Le `CustomVisual` type peut ensuite être inscrit auprès des classes de convertisseur, [`Button`](xref:Xamarin.Forms.Button) ce qui permet aux objets d’accepter d’utiliser les convertisseurs.

## <a name="register-the-ivisual-type"></a>Inscrire le type IVisual

Dans les projets de plateforme, décorez les espaces de noms `ExportRendererAttribute`du convertisseur avec les éléments suivants:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
namespace VisualDemos.iOS
{
    public class CustomButtonRenderer : ButtonRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
        {
            // ...
        }
    }
}
```

Dans cet exemple pour le projet de plateforme iOS, `ExportRendererAttribute` le spécifie `CustomButtonRenderer` que la classe sera [`Button`](xref:Xamarin.Forms.Button) utilisée pour restituer des objets consommatrices `IVisual` , avec le type inscrit comme troisième argument. Un convertisseur qui spécifie un `IVisual` type, dans le cadre de `ExportRendererAttribute`son, sera utilisé pour le rendu de l’option choisi dans les vues, plutôt que dans le convertisseur par défaut.

## <a name="consume-the-visual-renderer"></a>Utiliser le convertisseur visuel

Un [`Button`](xref:Xamarin.Forms.Button) objet peut s’abonner à l’utilisation des classes de convertisseur [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) en affectant à `Custom`sa propriété la valeur:

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> En XAML, un convertisseur de type supprime la nécessité d’inclure le suffixe «visuel» [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) dans la valeur de la propriété. Toutefois, le nom de type complet peut également être spécifié.

Le code C# équivalent est :

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

Au moment de la sélection du convertisseur [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) , la propriété [`Button`](xref:Xamarin.Forms.Button) du est inspectée et incluse dans le processus de sélection du convertisseur. Si un convertisseur n’est pas localisé, le convertisseur par défaut Xamarin. Forms sera utilisé.

Les captures d’écran suivantes montrent [`Button`](xref:Xamarin.Forms.Button)le rendu, qui affiche son texte avec une ombre:

[![Capture d’écran du bouton personnalisé avec texte ombré, sur iOS et Android](material-visual-images/custom-button.png "Bouton avec texte ombré")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>Inscrire un nom pour le type IVisual

Peut être utilisé pour enregistrer éventuellement un nom différent pour le `IVisual` type. [`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute) Cette approche peut être utilisée pour résoudre les conflits de noms entre différentes bibliothèques visuelles, ou dans les situations où vous souhaitez simplement faire référence à un visuel par un nom différent de son nom de type.

[`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute) Doit être défini au niveau de l’assembly dans la bibliothèque multiplateforme, ou dans le projet de plateforme:

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

Le `IVisual` type peut ensuite être consommé via son nom inscrit:

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> Quand vous consommez un visuel via son nom inscrit, tout suffixe «visuel» doit être inclus.

## <a name="related-links"></a>Liens connexes

- [Élément visuel (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Élément visuel Xamarin. Forms](material-visual.md)
- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
