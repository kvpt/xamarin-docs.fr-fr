---
title: Xamarin. Forms réutilisable RoundEffect
description: RoundEffect est un effet réutilisable qui peut être appliqué à n’importe quel contrôle dérivant de VisualElement pour afficher le contrôle sous forme de cercle.
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
ms.openlocfilehash: 851ed7a2ad1c416b4d03d583b9d0aeb7f7774eea
ms.sourcegitcommit: aa7e9d2c370ba9cbc830f10b94b4cd4221fc5467
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73055959"
---
# <a name="xamarinforms-reusable-roundeffect"></a>Xamarin. Forms réutilisable RoundEffect

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

Le RoundEffect simplifie le rendu d’un contrôle qui dérive de VisualElement sous la forme d’un cercle. Cet effet peut être utilisé pour créer des images circulaires, des boutons et d’autres contrôles :

[captures d’écran ![RoundEffect sur iOS et Android](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>Créer un RoutingEffect partagé

Une classe Effect doit être créée dans le projet partagé pour créer un effet multiplateforme. L’exemple d’application crée une classe `RoundEffect` vide qui dérive de la classe `RoutingEffect` :

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

Le projet de plateforme Android définit une classe `RoundEffect` qui dérive de `PlatformEffect`. Cette classe est marquée avec des attributs `assembly` qui permettent à Xamarin. Forms de résoudre la classe Effect :

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

La plateforme Android utilise le concept de `OutlineProvider` pour définir les bords d’un contrôle. L’exemple de projet comprend une classe `CornerRadiusProvider` qui dérive de la classe `ViewOutlineProvider` :

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

Cette classe utilise les propriétés `Width` et `Height` de l’instance Xamarin. Forms `Element` pour calculer un rayon qui est la moitié de la dimension la plus petite.

Une fois qu’un fournisseur de plan est défini, la classe `RoundEffect` peut l’utiliser pour implémenter l’effet :

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

La méthode `OnAttached` est appelée quand l’effet est attaché à un élément. L’objet `OutlineProvider` existant est enregistré afin de pouvoir être restauré lorsque l’effet est détaché. Une nouvelle instance de l' `CornerRadiusOutlineProvider` est utilisée comme `OutlineProvider` et `ClipToOutline` a la valeur true pour découper des éléments vers les bordures de contour.

La méthode `OnDetatched` est appelée lorsque l’effet est supprimé d’un élément et restaure la valeur de `OutlineProvider` d’origine.

> [!NOTE]
> Selon le type d’élément, la propriété `Control` peut ou ne peut pas être null. Si la propriété `Control` n’a pas la valeur null, les angles arrondis peuvent être appliqués directement au contrôle. Toutefois, si la valeur est null, les angles arrondis doivent être appliqués à l’objet `Container`. Le champ `effectTarget` permet d’appliquer l’effet à l’objet approprié.

## <a name="implement-the-ios-effect"></a>Implémenter l’effet iOS

Le projet de plateforme iOS définit une classe `RoundEffect` qui dérive de `PlatformEffect`. Cette classe est marquée avec des attributs `assembly` qui permettent à Xamarin. Forms de résoudre la classe Effect :

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

Sur iOS, les contrôles ont une propriété `Layer`, qui a une propriété `CornerRadius`. L’implémentation de la classe `RoundEffect` sur iOS calcule le rayon d’angle approprié et met à jour la propriété de `CornerRadius` de la couche :

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

La méthode `CalculateRadius` calcule un rayon en fonction de la dimension minimale du `Element`Xamarin. Forms. La méthode `OnAttached` est appelée lorsque l’effet est attaché à un contrôle et met à jour la propriété de `CornerRadius` de la couche. Elle définit la propriété `ClipToBounds` sur `true` afin que les éléments qui se déplacent soient détourés des bordures du contrôle. La méthode `OnDetatched` est appelée quand l’effet est supprimé d’un contrôle et inverse ces modifications, en restaurant le rayon d’angle d’origine.

> [!NOTE]
> Selon le type d’élément, la propriété `Control` peut ou ne peut pas être null. Si la propriété `Control` n’a pas la valeur null, les angles arrondis peuvent être appliqués directement au contrôle. Toutefois, si la valeur est null, les angles arrondis doivent être appliqués à l’objet `Container`. Le champ `effectTarget` permet d’appliquer l’effet à l’objet approprié.

## <a name="consume-the-effect"></a>Utiliser l’effet

Une fois l’effet implémenté sur les plateformes, il peut être consommé par les contrôles Xamarin. Forms. Une application courante du `RoundEffect` consiste à créer un objet `Image` circulaire. Le code XAML suivant montre l’effet appliqué à une instance de `Image` :

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

La classe `RoundEffect` peut être appliquée à n’importe quel contrôle qui dérive de `VisualElement`.

> [!NOTE]
> Pour l’effet de calculer le rayon correct, le contrôle auquel il est appliqué doit avoir un dimensionnement explicite. Par conséquent, les propriétés `HeightRequest` et `WidthRequest` doivent être définies. Si le contrôle affecté apparaît dans un `StackLayout`, sa propriété `HorizontalOptions` ne doit pas utiliser l’une des valeurs de **développement** telles que `LayoutOptions.CenterAndExpand` ou il n’a pas de dimensions exactes.

## <a name="related-links"></a>Liens connexes

- [Exemple d’application RoundEffect](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [Présentation des effets](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [Création d’un effet](~/xamarin-forms/app-fundamentals/effects/creating.md)
