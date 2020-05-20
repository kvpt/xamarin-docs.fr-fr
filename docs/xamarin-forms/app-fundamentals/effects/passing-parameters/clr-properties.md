---
title: Passer des paramètres d’effet en tant que propriétés CLR (Common Language Runtime)
description: Les propriétés CLR (Common Language Runtime) permettent de définir des paramètres d’effet qui ne répondent pas aux changements apportés aux propriétés au moment de l’exécution. Cet article montre comment utiliser des propriétés CLR pour passer des paramètres à un effet.
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 63c7eb799b92871d7e00b7302bbe86f8b959ec23
ms.sourcegitcommit: 05ba8ffb8b34ec881b89e442323f3edd8de18f2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83546035"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Passer des paramètres d’effet en tant que propriétés CLR (Common Language Runtime)

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)

_Les propriétés CLR (Common Language Runtime) peuvent être utilisées pour définir des paramètres Effects qui ne répondent pas aux modifications de propriété d’exécution. Cet article montre comment utiliser les propriétés CLR pour passer des paramètres à un effet._

Pour créer des paramètres d’effet qui ne répondent pas aux changements apportés aux propriétés au moment de l’exécution, suivez ces étapes :

1. Créez une `public` classe qui sous-classe la [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe. La classe `RoutingEffect` représente un effet indépendant de la plateforme qui wrappe un effet interne généralement propre à une plateforme.
1. Créez un constructeur qui appelle le constructeur de classe de base, en passant une concaténation du nom du groupe de résolution, et l’ID unique qui a été spécifié sur la classe d’effet propre à chaque plateforme.
1. Ajoutez des propriétés à la classe pour chaque paramètre à passer à l’effet.

Les paramètres peuvent ensuite être passés à l’effet en spécifiant des valeurs pour chaque propriété lors de l’instanciation de l’effet.

L’exemple d’application montre un `ShadowEffect` qui ajoute une ombre au texte affiché par un [`Label`](xref:Xamarin.Forms.Label) contrôle. Le diagramme suivant illustre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](clr-properties-images/shadow-effect.png "Shadow Effect Project Responsibilities")

Un [`Label`](xref:Xamarin.Forms.Label) contrôle sur le `HomePage` est personnalisé par `LabelShadowEffect` dans chaque projet spécifique à la plateforme. Les paramètres sont passés à chaque `LabelShadowEffect` par le biais des propriétés dans la classe `ShadowEffect`. Chaque classe `LabelShadowEffect` dérive de la classe `PlatformEffect` pour chaque plateforme. Il en résulte l’ajout d’une ombre au texte affiché par le contrôle `Label`, comme illustré dans les captures d’écran suivantes :

![](clr-properties-images/screenshots.png "Shadow Effect on each Platform")

## <a name="creating-effect-parameters"></a>Création de paramètres d’effet

Une `public` classe qui sous-classe la [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe doit être créée pour représenter des paramètres Effects, comme illustré dans l’exemple de code suivant :

```csharp
public class ShadowEffect : RoutingEffect
{
  public float Radius { get; set; }

  public Color Color { get; set; }

  public float DistanceX { get; set; }

  public float DistanceY { get; set; }

  public ShadowEffect () : base ("MyCompany.LabelShadowEffect")
  {            
  }
}
```

`LabelShadowEffect` contient quatre propriétés qui représentent les paramètres à passer à chaque `ShadowEffect` propre à la plateforme. Le constructeur de classe appelle le constructeur de classe de base, en passant un paramètre constitué d’une concaténation du nom du groupe de résolution, et l’ID unique qui a été spécifié sur la classe d’effet propre à chaque plateforme. Par conséquent, une nouvelle instance de `MyCompany.LabelShadowEffect` sera ajoutée à la collection d’un [`Effects`](xref:Xamarin.Forms.Element.Effects) contrôle lorsqu’un `ShadowEffect` est instancié.

## <a name="consuming-the-effect"></a>Consommation de l’effet

L’exemple de code XAML suivant montre un [`Label`](xref:Xamarin.Forms.Label) contrôle auquel `ShadowEffect` est attaché :

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Effects>
    <local:ShadowEffect Radius="5" DistanceX="5" DistanceY="5">
      <local:ShadowEffect.Color>
        <OnPlatform x:TypeArguments="Color">
            <On Platform="iOS" Value="Black" />
            <On Platform="Android" Value="White" />
            <On Platform="UWP" Value="Red" />
        </OnPlatform>
      </local:ShadowEffect.Color>
    </local:ShadowEffect>
  </Label.Effects>
</Label>
```

L’équivalent [`Label`](xref:Xamarin.Forms.Label) en C# est illustré dans l’exemple de code suivant :

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};

Color color = Color.Default;
switch (Device.RuntimePlatform)
{
    case Device.iOS:
        color = Color.Black;
        break;
    case Device.Android:
        color = Color.White;
        break;
    case Device.UWP:
        color = Color.Red;
        break;
}

label.Effects.Add (new ShadowEffect {
  Radius = 5,
  Color = color,
  DistanceX = 5,
  DistanceY = 5
});
```

Dans les deux exemples de code, une instance de la `ShadowEffect` classe est instanciée avec les valeurs spécifiées pour chaque propriété, avant d’être ajoutée à la collection du contrôle [`Effects`](xref:Xamarin.Forms.Element.Effects) . Notez que la propriété `ShadowEffect.Color` utilise des valeurs de couleur différentes pour chaque plateforme. Pour plus d’informations, consultez l’article sur la [classe Device](~/xamarin-forms/platform/device.md).

## <a name="creating-the-effect-on-each-platform"></a>Création de l’effet sur chaque plateforme

Les sections suivantes décrivent l’implémentation de la classe `LabelShadowEffect` pour chaque plateforme.

### <a name="ios-project"></a>Projet iOS

L’exemple de code suivant illustre l’implémentation de `LabelShadowEffect` pour le projet iOS :

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    Control.Layer.ShadowRadius = effect.Radius;
                    Control.Layer.ShadowColor = effect.Color.ToCGColor ();
                    Control.Layer.ShadowOffset = new CGSize (effect.DistanceX, effect.DistanceY);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

La méthode `OnAttached` récupère l’instance `ShadowEffect` et définit les propriétés `Control.Layer` sur les valeurs de propriété spécifiées pour créer l’ombre. Cette fonctionnalité est wrappée dans un bloc `try`/`catch` au cas où le contrôle auquel l’effet est joint n’a pas les propriétés `Control.Layer`. Aucune implémentation n’est fournie par la méthode `OnDetached`, car aucun nettoyage n’est nécessaire.

### <a name="android-project"></a>Projet Android

L’exemple de code suivant illustre l’implémentation de `LabelShadowEffect` pour le projet Android :

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var control = Control as Android.Widget.TextView;
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    float radius = effect.Radius;
                    float distanceX = effect.DistanceX;
                    float distanceY = effect.DistanceY;
                    Android.Graphics.Color color = effect.Color.ToAndroid ();
                    control.SetShadowLayer (radius, distanceX, distanceY, color);
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

La `OnAttached` méthode récupère l' `ShadowEffect` instance et appelle la [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) méthode pour créer une ombre à l’aide des valeurs de propriété spécifiées. Cette fonctionnalité est wrappée dans un bloc `try`/`catch` au cas où le contrôle auquel l’effet est joint n’a pas les propriétés `Control.Layer`. Aucune implémentation n’est fournie par la méthode `OnDetached`, car aucun nettoyage n’est nécessaire.

### <a name="universal-windows-platform-project"></a>Projet de plateforme Windows universelle

L’exemple de code suivant montre l’implémentation de `LabelShadowEffect` pour le projet de plateforme Windows universelle (UWP) :

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                    if (effect != null) {
                        var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;
                        var shadowLabel = new Label ();
                        shadowLabel.Text = textBlock.Text;
                        shadowLabel.FontAttributes = FontAttributes.Bold;
                        shadowLabel.HorizontalOptions = LayoutOptions.Center;
                        shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;
                        shadowLabel.TextColor = effect.Color;
                        shadowLabel.TranslationX = effect.DistanceX;
                        shadowLabel.TranslationY = effect.DistanceY;

                        ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                        shadowAdded = true;
                    }
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

La plateforme Windows universelle ne fournit pas d’effet d’ombre, et l' `LabelShadowEffect` implémentation sur les deux plates-formes en simule une en ajoutant un deuxième décalage [`Label`](xref:Xamarin.Forms.Label) derrière le réplica principal `Label` . La méthode `OnAttached` récupère l’instance `ShadowEffect`, crée le nouveau contrôle `Label` et définit certaines propriétés de disposition sur `Label`. Il crée ensuite l’ombre en définissant [`TextColor`](xref:Xamarin.Forms.Label.TextColor) les [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) Propriétés, et [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) pour contrôler la couleur et l’emplacement du `Label` . Le contrôle `shadowLabel` est alors inséré en décalage derrière le contrôle `Label` principal. Cette fonctionnalité est wrappée dans un bloc `try`/`catch` au cas où le contrôle auquel l’effet est joint n’a pas les propriétés `Control.Layer`. Aucune implémentation n’est fournie par la méthode `OnDetached`, car aucun nettoyage n’est nécessaire.

## <a name="summary"></a>Résumé

Cet article a montré comment utiliser des propriétés CLR pour passer des paramètres à un effet. Les propriétés CLR permettent de définir des paramètres d’effet qui ne répondent pas aux changements apportés aux propriétés au moment de l’exécution.

## <a name="related-links"></a>Liens connexes

- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Résultat](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Effet d’ombre (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)
