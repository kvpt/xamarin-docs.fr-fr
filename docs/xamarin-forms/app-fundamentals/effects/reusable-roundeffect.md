---
title: Xamarin.Forms Réutilisable RoundEffect
description: RoundEffect est un effet réutilisable qui peut être appliqué à n’importe quel contrôle provenant de VisualElement pour rendre le contrôle en tant que cercle.
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
ms.openlocfilehash: 851ed7a2ad1c416b4d03d583b9d0aeb7f7774eea
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73055959"
---
# <a name="xamarinforms-reusable-roundeffect"></a>Xamarin.Forms Réutilisable RoundEffect

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

Le RoundEffect simplifie tout contrôle qui dérive de VisualElement en tant que cercle. Cet effet peut être utilisé pour créer des images circulaires, des boutons et d’autres contrôles :

[![Captures d’écran RoundEffect sur iOS et Android](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>Créer un RoutingEffect partagé

Une classe d’effet doit être créée dans le projet partagé pour créer un effet cross-platform. L’application d’échantillon crée une classe vide `RoundEffect` qui dérive de la `RoutingEffect` classe :

```csharp
public class RoundEffect : RoutingEffect
{
    public RoundEffect() : base($"Xamarin.{nameof(RoundEffect)}")
    {
    }
}
```

Cette classe permet au projet partagé de résoudre les références à l’effet dans le code ou XAML, mais ne fournit aucune fonctionnalité. L’effet doit avoir des implémentations pour chaque plate-forme.

## <a name="implement-the-android-effect"></a>Implémenter l’effet Android

Le projet de plate-forme Android définit une `RoundEffect` classe qui dérive de `PlatformEffect`. Cette classe est `assembly` marquée avec des attributs qui permettent à Xamarin.Forms de résoudre la classe d’effet :

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.Droid.RoundEffect), nameof(RoundEffectDemo.Droid.RoundEffect))]
namespace RoundEffectDemo.Droid
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
}
```

La plate-forme Android `OutlineProvider` utilise le concept d’un pour définir les bords d’un contrôle. Le projet d’échantillon comprend `CornerRadiusProvider` une `ViewOutlineProvider` classe qui dérive de la classe :

```csharp
class CornerRadiusOutlineProvider : ViewOutlineProvider
{
    Element element;

    public CornerRadiusOutlineProvider(Element formsElement)
    {
        element = formsElement;
    }

    public override void GetOutline(Android.Views.View view, Outline outline)
    {
        float scale = view.Resources.DisplayMetrics.Density;
        double width = (double)element.GetValue(VisualElement.WidthProperty) * scale;
        double height = (double)element.GetValue(VisualElement.HeightProperty) * scale;
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;
        Rect rect = new Rect(0, 0, (int)width, (int)height);
        outline.SetRoundRect(rect, radius);
    }
}
```

Cette classe `Width` utilise `Height` l’instance Xamarin.Forms `Element` pour calculer un rayon qui est la moitié de la dimension la plus courte.

Une fois qu’un `RoundEffect` fournisseur de contours est défini, la classe peut la consommer pour mettre en œuvre l’effet :

```csharp
public class RoundEffect : PlatformEffect
{
    ViewOutlineProvider originalProvider;
    Android.Views.View effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalProvider = effectTarget.OutlineProvider;
            effectTarget.OutlineProvider = new CornerRadiusOutlineProvider(Element);
            effectTarget.ClipToOutline = true;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if(effectTarget != null)
        {
            effectTarget.OutlineProvider = originalProvider;
            effectTarget.ClipToOutline = false;
        }
    }
}
```

La `OnAttached` méthode est appelée lorsque l’effet est attaché à un élément. L’objet existant `OutlineProvider` est enregistré afin qu’il puisse être restauré lorsque l’effet est détaché. Une nouvelle instance `CornerRadiusOutlineProvider` de la `OutlineProvider` `ClipToOutline` est utilisée comme le et est mis à vrai pour couper les éléments débordants aux bordures de contour.

La `OnDetatched` méthode est appelée lorsque l’effet est supprimé `OutlineProvider` d’un élément et restaure la valeur d’origine.

> [!NOTE]
> Selon le type d’élément, la `Control` propriété peut ou non être nulle. Si `Control` la propriété n’est pas nulle, les coins arrondis peuvent être appliqués directement au contrôle. Toutefois, s’il est nul, les `Container` coins arrondis doivent être appliqués à l’objet. Le `effectTarget` champ permet d’appliquer l’effet à l’objet approprié.

## <a name="implement-the-ios-effect"></a>Mettre en œuvre l’effet iOS

Le projet de plate-forme iOS définit une `RoundEffect` classe qui dérive de `PlatformEffect`. Cette classe est `assembly` marquée avec des attributs qui permettent à Xamarin.Forms de résoudre la classe d’effet :

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.iOS.RoundEffect), nameof(RoundEffectDemo.iOS.RoundEffect))]
namespace RoundEffectDemo.iOS
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
```

Sur iOS, les `Layer` contrôles ont `CornerRadius` une propriété, qui a une propriété. La `RoundEffect` mise en œuvre de la classe sur `CornerRadius` iOS calcule le rayon d’angle approprié et met à jour la propriété de la couche :

```csharp
public class RoundEffect : PlatformEffect
{
    nfloat originalRadius;
    UIKit.UIView effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalRadius = effectTarget.Layer.CornerRadius;
            effectTarget.ClipsToBounds = true;
            effectTarget.Layer.CornerRadius = CalculateRadius();
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if (effectTarget != null)
        {
            effectTarget.ClipsToBounds = false;
            if (effectTarget.Layer != null)
            {
                effectTarget.Layer.CornerRadius = originalRadius;
            }
        }
    }

    float CalculateRadius()
    {
        double width = (double)Element.GetValue(VisualElement.WidthRequestProperty);
        double height = (double)Element.GetValue(VisualElement.HeightRequestProperty);
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;

        return radius;
    }
}
```

La `CalculateRadius` méthode calcule un rayon basé sur la dimension `Element`minimale du Xamarin.Forms . La `OnAttached` méthode est appelée lorsque l’effet est attaché à `CornerRadius` un contrôle, et met à jour la propriété de la couche. Il définit `ClipToBounds` la `true` propriété à des éléments si débordants sont coupés aux frontières du contrôle. La `OnDetatched` méthode est appelée lorsque l’effet est retiré d’un contrôle et inverse ces changements, la restauration du rayon d’angle d’origine.

> [!NOTE]
> Selon le type d’élément, la `Control` propriété peut ou non être nulle. Si `Control` la propriété n’est pas nulle, les coins arrondis peuvent être appliqués directement au contrôle. Toutefois, s’il est nul, les `Container` coins arrondis doivent être appliqués à l’objet. Le `effectTarget` champ permet d’appliquer l’effet à l’objet approprié.

## <a name="consume-the-effect"></a>Consommer l’effet

Une fois que l’effet est implémenté sur toutes les plates-formes, il peut être consommé par les contrôles Xamarin.Forms. Une application commune `RoundEffect` de `Image` la fait une circulaire objet. Le XAML suivant montre l’effet appliqué à une `Image` instance :

```xaml
<Image Source=outdoors"
       HeightRequest="100"
       WidthRequest="100">
    <Image.Effects>
        <local:RoundEffect />
    </Image.Effects>
</Image>
```

L’effet peut également être appliqué dans le code :

```csharp
var image = new Image
{
    Source = ImageSource.FromFile("outdoors"),
    HeightRequest = 100,
    WidthRequest = 100
};
image.Effects.Add(new RoundEffect());
```

La `RoundEffect` classe peut être appliquée à `VisualElement`n’importe quel contrôle qui dérive de .

> [!NOTE]
> Pour que l’effet de calculer le rayon correct, le contrôle qu’il est appliqué à doit avoir le dimensionnement explicite. Par conséquent, les `HeightRequest` propriétés et `WidthRequest` les propriétés doivent être définies. Si le contrôle affecté `StackLayout`apparaît `HorizontalOptions` dans un , sa propriété `LayoutOptions.CenterAndExpand` ne doit pas utiliser l’une des valeurs **d’extension** telles que ou il n’aura pas de dimensions précises.

## <a name="related-links"></a>Liens connexes

- [Application d’échantillon RoundEffect](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [Présentation des effets](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [Création d’un effet](~/xamarin-forms/app-fundamentals/effects/creating.md)
