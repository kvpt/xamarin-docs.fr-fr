---
title: Xamarin.FormsRoundEffect réutilisable
description: RoundEffect est un effet réutilisable qui peut être appliqué à n’importe quel contrôle dérivant de VisualElement pour afficher le contrôle sous forme de cercle.
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fc3776934a4c109b2527132b11c6c6a93b7d9f9e
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138851"
---
# <a name="xamarinforms-reusable-roundeffect"></a>Xamarin.FormsRoundEffect réutilisable

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

Le RoundEffect simplifie le rendu d’un contrôle qui dérive de VisualElement sous la forme d’un cercle. Cet effet peut être utilisé pour créer des images circulaires, des boutons et d’autres contrôles :

[![Captures d’écran RoundEffect sur iOS et Android](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>Créer un RoutingEffect partagé

Une classe Effect doit être créée dans le projet partagé pour créer un effet multiplateforme. L’exemple d’application crée une `RoundEffect` classe vide qui dérive de la `RoutingEffect` classe :

```csharp
public class RoundEffect : RoutingEffect
{
    public RoundEffect() : base($"Xamarin.{nameof(RoundEffect)}")
    {
    }
}
```

Cette classe permet au projet partagé de résoudre les références à l’effet dans le code ou le XAML, mais ne fournit aucune fonctionnalité. L’effet doit avoir des implémentations pour chaque plateforme.

## <a name="implement-the-android-effect"></a>Implémenter l’effet Android

Le projet de plateforme Android définit une `RoundEffect` classe qui dérive de `PlatformEffect` . Cette classe est balisée avec des `assembly` attributs qui permettent Xamarin.Forms de résoudre la classe Effect :

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

La plateforme Android utilise le concept de `OutlineProvider` pour définir les bords d’un contrôle. L’exemple de projet comprend une `CornerRadiusProvider` classe qui dérive de la `ViewOutlineProvider` classe :

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

Cette classe utilise les `Width` `Height` Propriétés et de l' Xamarin.Forms `Element` instance pour calculer un rayon qui est la moitié de la dimension la plus petite.

Une fois qu’un fournisseur de plan est défini `RoundEffect` , la classe peut l’utiliser pour implémenter l’effet :

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

La `OnAttached` méthode est appelée lorsque l’effet est attaché à un élément. L' `OutlineProvider` objet existant est enregistré afin de pouvoir être restauré lorsque l’effet est détaché. Une nouvelle instance de `CornerRadiusOutlineProvider` est utilisée en tant que `OutlineProvider` et `ClipToOutline` a la valeur true pour découper des éléments vers les bordures de contour.

La `OnDetatched` méthode est appelée lorsque l’effet est supprimé d’un élément et restaure la valeur d’origine `OutlineProvider` .

> [!NOTE]
> Selon le type d’élément, la `Control` propriété peut avoir la valeur ou ne peut pas être null. Si la `Control` propriété n’a pas la valeur null, les angles arrondis peuvent être appliqués directement au contrôle. Toutefois, si la valeur est null, les angles arrondis doivent être appliqués à l' `Container` objet. Le `effectTarget` champ permet d’appliquer l’effet à l’objet approprié.

## <a name="implement-the-ios-effect"></a>Implémenter l’effet iOS

Le projet de plateforme iOS définit une `RoundEffect` classe qui dérive de `PlatformEffect` . Cette classe est balisée avec des `assembly` attributs qui permettent Xamarin.Forms de résoudre la classe Effect :

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

Sur iOS, les contrôles ont une `Layer` propriété, qui a une `CornerRadius` propriété. L' `RoundEffect` implémentation de la classe sur iOS calcule le rayon d’angle approprié et met à jour la propriété de la couche `CornerRadius` :

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

La `CalculateRadius` méthode calcule un rayon en fonction de la dimension minimale de Xamarin.Forms `Element` . La `OnAttached` méthode est appelée lorsque l’effet est attaché à un contrôle et met à jour la propriété de la couche `CornerRadius` . Elle définit la `ClipToBounds` propriété de `true` sorte que les éléments de dépassement de capacité soient détourés des bordures du contrôle. La `OnDetatched` méthode est appelée lorsque l’effet est supprimé d’un contrôle et inverse ces modifications, en restaurant le rayon d’angle d’origine.

> [!NOTE]
> Selon le type d’élément, la `Control` propriété peut avoir la valeur ou ne peut pas être null. Si la `Control` propriété n’a pas la valeur null, les angles arrondis peuvent être appliqués directement au contrôle. Toutefois, si la valeur est null, les angles arrondis doivent être appliqués à l' `Container` objet. Le `effectTarget` champ permet d’appliquer l’effet à l’objet approprié.

## <a name="consume-the-effect"></a>Utiliser l’effet

Une fois l’effet implémenté sur les plateformes, il peut être consommé par les Xamarin.Forms contrôles. Une application courante du `RoundEffect` consiste à créer un `Image` objet circulaire. Le code XAML suivant montre l’effet appliqué à une `Image` instance :

```xaml
<Image Source=outdoors"
       HeightRequest="100"
       WidthRequest="100">
    <Image.Effects>
        <local:RoundEffect />
    </Image.Effects>
</Image>
```

L’effet peut également être appliqué dans le code :

```csharp
var image = new Image
{
    Source = ImageSource.FromFile("outdoors"),
    HeightRequest = 100,
    WidthRequest = 100
};
image.Effects.Add(new RoundEffect());
```

La `RoundEffect` classe peut être appliquée à n’importe quel contrôle qui dérive de `VisualElement` .

> [!NOTE]
> Pour l’effet de calculer le rayon correct, le contrôle auquel il est appliqué doit avoir un dimensionnement explicite. Par conséquent, `HeightRequest` les `WidthRequest` Propriétés et doivent être définies. Si le contrôle affecté apparaît dans un `StackLayout` , sa `HorizontalOptions` propriété ne doit pas utiliser l’une des valeurs de **développement** telles que `LayoutOptions.CenterAndExpand` ou il n’a pas de dimensions exactes.

## <a name="related-links"></a>Liens connexes

- [Exemple d’application RoundEffect](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [Présentation des effets](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [Création d’un effet](~/xamarin-forms/app-fundamentals/effects/creating.md)
