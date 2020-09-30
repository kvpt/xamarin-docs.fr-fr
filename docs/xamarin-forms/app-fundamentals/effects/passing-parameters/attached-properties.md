---
title: Passer des paramètres d’effet en tant que propriétés jointes
description: Les propriétés jointes permettent de définir des paramètres d’effet qui répondent aux changements apportés aux propriétés au moment de l’exécution. Cet article décrit comment utiliser des propriétés jointes pour passer des paramètres à un effet et comment changer un paramètre au moment de l’exécution.
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c014396f9670ece93295c2c8da1738f9dc7dae9e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555981"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>Passer des paramètres d’effet en tant que propriétés jointes

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange)

_Les propriétés jointes peuvent être utilisées pour définir des paramètres d’effet qui répondent aux modifications de propriété d’exécution. Cet article montre comment utiliser des propriétés jointes pour passer des paramètres à un effet et comment modifier un paramètre au moment de l’exécution._

Pour créer des paramètres d’effet qui répondent aux changements apportés aux propriétés au moment de l’exécution, suivez ces étapes :

1. Créez une classe `static` qui contient une propriété jointe pour chaque paramètre à passer à l’effet.
1. Ajoutez une propriété jointe supplémentaire à la classe qui contrôlera l’ajout ou la suppression de l’effet sur le contrôle auquel la classe sera jointe. Assurez-vous que cette propriété jointe inscrit un délégué `propertyChanged` qui sera exécuté au changement de la valeur de la propriété.
1. Créez des méthodes getter et setter `static` pour chaque propriété jointe.
1. Dans le délégué `propertyChanged`, implémentez la logique qui ajoute ou supprime l’effet.
1. Implémentez une classe imbriquée dans la classe `static`, nommée d’après l’effet, pour sous-classer la classe `RoutingEffect`. Pour le constructeur, appelez le constructeur de classe de base, en passant une concaténation du nom du groupe de résolution, et l’ID unique qui a été spécifié sur la classe d’effet propre à chaque plateforme.

Les paramètres peuvent ensuite être passés à l’effet en ajoutant les propriétés jointes, et les valeurs de propriété, au contrôle approprié. En outre, les paramètres peuvent être changés au moment de l’exécution en spécifiant une nouvelle valeur de propriété jointe.

> [!NOTE]
> Une propriété jointe est un type spécial de propriété pouvant être liée, définie dans une seule classe mais jointe à d’autres objets, et représentée en XAML sous forme d’attributs contenant une classe et un nom de propriété séparés par un point. Pour plus d’informations, consultez [Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md).

L’exemple d’application montre un `ShadowEffect` qui ajoute une ombre au texte affiché par un [`Label`](xref:Xamarin.Forms.Label) contrôle. La couleur de l’ombre peut également être changée au moment de l’exécution. Le diagramme suivant illustre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![Responsabilités de projet ShadowEffect](attached-properties-images/shadow-effect.png)

Un [`Label`](xref:Xamarin.Forms.Label) contrôle sur le `HomePage` est personnalisé par `LabelShadowEffect` dans chaque projet spécifique à la plateforme. Les paramètres sont passés à chaque `LabelShadowEffect` par le biais des propriétés jointes dans la classe `ShadowEffect`. Chaque classe `LabelShadowEffect` dérive de la classe `PlatformEffect` pour chaque plateforme. Il en résulte l’ajout d’une ombre au texte affiché par le contrôle `Label`, comme illustré dans les captures d’écran suivantes :

![Effet d’ombre sur chaque plateforme](attached-properties-images/screenshots.png)

## <a name="creating-effect-parameters"></a>Création de paramètres d’effet

Une classe `static` doit être créée pour représenter les paramètres d’effet, comme illustré dans l’exemple de code suivant :

```csharp
public static class ShadowEffect
{
  public static readonly BindableProperty HasShadowProperty =
    BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false, propertyChanged: OnHasShadowChanged);
  public static readonly BindableProperty ColorProperty =
    BindableProperty.CreateAttached ("Color", typeof(Color), typeof(ShadowEffect), Color.Default);
  public static readonly BindableProperty RadiusProperty =
    BindableProperty.CreateAttached ("Radius", typeof(double), typeof(ShadowEffect), 1.0);
  public static readonly BindableProperty DistanceXProperty =
    BindableProperty.CreateAttached ("DistanceX", typeof(double), typeof(ShadowEffect), 0.0);
  public static readonly BindableProperty DistanceYProperty =
    BindableProperty.CreateAttached ("DistanceY", typeof(double), typeof(ShadowEffect), 0.0);

  public static bool GetHasShadow (BindableObject view)
  {
    return (bool)view.GetValue (HasShadowProperty);
  }

  public static void SetHasShadow (BindableObject view, bool value)
  {
    view.SetValue (HasShadowProperty, value);
  }
  ...

  static void OnHasShadowChanged (BindableObject bindable, object oldValue, object newValue)
  {
    var view = bindable as View;
    if (view == null) {
      return;
    }

    bool hasShadow = (bool)newValue;
    if (hasShadow) {
      view.Effects.Add (new LabelShadowEffect ());
    } else {
      var toRemove = view.Effects.FirstOrDefault (e => e is LabelShadowEffect);
      if (toRemove != null) {
        view.Effects.Remove (toRemove);
      }
    }
  }

  class LabelShadowEffect : RoutingEffect
  {
    public LabelShadowEffect () : base ("MyCompany.LabelShadowEffect")
    {
    }
  }
}
```

La classe `ShadowEffect` contient cinq propriétés jointes, elles-mêmes ayant chacune des méthodes setter et getter `static`. Quatre de ces propriétés représentent les paramètres à passer à chaque `LabelShadowEffect` propre à la plateforme. La classe `ShadowEffect` définit également une propriété jointe `HasShadow` qui contrôle l’ajout ou la suppression de l’effet sur le contrôle auquel la classe `ShadowEffect` est jointe. Cette propriété jointe inscrit la méthode `OnHasShadowChanged` qui est exécutée au changement de la valeur de la propriété. Cette méthode ajoute ou supprime l’effet en fonction de la valeur de la propriété jointe `HasShadow`.

La classe imbriquée `LabelShadowEffect` , qui sous-classe la [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe, prend en charge l’ajout et la suppression d’effets. La classe `RoutingEffect` représente un effet indépendant de la plateforme qui wrappe un effet interne généralement propre à une plateforme. Cela simplifie le processus de suppression de l’effet, car il n’y a pas d’accès au moment de la compilation aux informations de type pour un effet propre à la plateforme. Le constructeur `LabelShadowEffect` appelle le constructeur de classe de base, en passant un paramètre constitué d’une concaténation du nom du groupe de résolution, et l’ID unique qui a été spécifié sur la classe d’effet propre à chaque plateforme. Cela permet l’ajout et la suppression de l’effet dans la méthode `OnHasShadowChanged` de la façon suivante :

- **Ajout de l’effet** : une nouvelle instance de `LabelShadowEffect` est ajoutée à la collection [`Effects`](xref:Xamarin.Forms.Element.Effects) du contrôle. Cela remplace l’utilisation [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) de la méthode pour ajouter l’effet.
- **Suppression de l’effet** : la première instance de `LabelShadowEffect` dans la collection [`Effects`](xref:Xamarin.Forms.Element.Effects) du contrôle est retirée et supprimée.

## <a name="consuming-the-effect"></a>Consommation de l’effet

Chaque spécifique à `LabelShadowEffect` la plateforme peut être consommé en ajoutant les propriétés jointes à un [`Label`](xref:Xamarin.Forms.Label) contrôle, comme illustré dans l’exemple de code XAML suivant :

```xaml
<Label Text="Label Shadow Effect" ...
       local:ShadowEffect.HasShadow="true" local:ShadowEffect.Radius="5"
       local:ShadowEffect.DistanceX="5" local:ShadowEffect.DistanceY="5">
  <local:ShadowEffect.Color>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="Black" />
        <On Platform="Android" Value="White" />
        <On Platform="UWP" Value="Red" />
    </OnPlatform>
  </local:ShadowEffect.Color>
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

ShadowEffect.SetHasShadow (label, true);
ShadowEffect.SetRadius (label, 5);
ShadowEffect.SetDistanceX (label, 5);
ShadowEffect.SetDistanceY (label, 5);
ShadowEffect.SetColor (label, color));
```

L’affectation de la valeur à la `ShadowEffect.HasShadow` propriété jointe `true` exécute la `ShadowEffect.OnHasShadowChanged` méthode qui ajoute ou supprime le du `LabelShadowEffect` [`Label`](xref:Xamarin.Forms.Label) contrôle. Dans les deux exemples de code, la propriété jointe `ShadowEffect.Color` définit des valeurs de couleur différentes pour chaque plateforme. Pour plus d’informations, consultez l’article sur la [classe Device](~/xamarin-forms/platform/device.md).

En outre, un [`Button`](xref:Xamarin.Forms.Button) permet de modifier la couleur de l’ombre au moment de l’exécution. En cas de clic sur `Button`, le code suivant change la couleur de l’ombre en définissant la propriété jointe `ShadowEffect.Color` :

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>Consommation de l’effet avec un style

Les effets qui peuvent être consommés en ajoutant des propriétés jointes à un contrôle peuvent également être consommés par un style. L’exemple de code XAML suivant montre un style *explicite* pour l’effet d’ombre, applicable aux contrôles [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="True" />
    <Setter Property="local:ShadowEffect.Radius" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceX" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceY" Value="5" />
  </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style)Peut être appliqué à un [`Label`](xref:Xamarin.Forms.Label) en affectant à sa [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriété l’instance à l’aide de `Style` l’extension de `StaticResource` balisage, comme illustré dans l’exemple de code suivant :

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

Pour plus d’informations sur les styles, consultez [Styles](~/xamarin-forms/user-interface/styles/index.md).

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
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                Control.Layer.ShadowOpacity = 1.0f;
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateRadius ()
        {
            Control.Layer.ShadowRadius = (nfloat)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            Control.Layer.ShadowColor = ShadowEffect.GetColor (Element).ToCGColor ();
        }

        void UpdateOffset ()
        {
            Control.Layer.ShadowOffset = new CGSize (
                (double)ShadowEffect.GetDistanceX (Element),
                (double)ShadowEffect.GetDistanceY (Element));
        }
    }
```

La méthode `OnAttached` appelle les méthodes qui récupèrent les valeurs de propriété jointe à l’aide des méthodes getter `ShadowEffect`, et qui définissent les propriétés `Control.Layer` sur les valeurs de propriété pour créer l’ombre. Cette fonctionnalité est wrappée dans un bloc `try`/`catch` au cas où le contrôle auquel l’effet est joint n’a pas les propriétés `Control.Layer`. Aucune implémentation n’est fournie par la méthode `OnDetached`, car aucun nettoyage n’est nécessaire.

#### <a name="responding-to-property-changes"></a>Répondre aux changements apportés aux propriétés

Si l’une des valeurs de la propriété jointe `ShadowEffect` change au moment de l’exécution, l’effet doit répondre en affichant les changements. Une version substituée de la méthode `OnElementPropertyChanged`, dans la classe d’effet propre à chaque plateforme, est utilisée pour répondre aux changements de la propriété pouvant être liée, comme illustré dans l’exemple de code suivant :

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

La méthode `OnElementPropertyChanged` met à jour le rayon, la couleur ou le décalage de l’ombre si la valeur de la propriété jointe `ShadowEffect` appropriée a changé. Une vérification de la propriété changée doit toujours être effectuée, car cette substitution peut être appelée plusieurs fois.

### <a name="android-project"></a>Projet Android

L’exemple de code suivant illustre l’implémentation de `LabelShadowEffect` pour le projet Android :

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        Android.Widget.TextView control;
        Android.Graphics.Color color;
        float radius, distanceX, distanceY;

        protected override void OnAttached ()
        {
            try {
                control = Control as Android.Widget.TextView;
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                UpdateControl ();
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateControl ()
        {
            if (control != null) {
                control.SetShadowLayer (radius, distanceX, distanceY, color);
            }
        }

        void UpdateRadius ()
        {
            radius = (float)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            color = ShadowEffect.GetColor (Element).ToAndroid ();
        }

        void UpdateOffset ()
        {
            distanceX = (float)ShadowEffect.GetDistanceX (Element);
            distanceY = (float)ShadowEffect.GetDistanceY (Element);
        }
    }
```

La `OnAttached` méthode appelle des méthodes qui récupèrent les valeurs de propriété jointes à l’aide des `ShadowEffect` accesseurs get et appelle une méthode qui appelle la [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) méthode pour créer une ombre à l’aide des valeurs de propriété. Cette fonctionnalité est wrappée dans un bloc `try`/`catch` au cas où le contrôle auquel l’effet est joint n’a pas les propriétés `Control.Layer`. Aucune implémentation n’est fournie par la méthode `OnDetached`, car aucun nettoyage n’est nécessaire.

#### <a name="responding-to-property-changes"></a>Répondre aux changements apportés aux propriétés

Si l’une des valeurs de la propriété jointe `ShadowEffect` change au moment de l’exécution, l’effet doit répondre en affichant les changements. Une version substituée de la méthode `OnElementPropertyChanged`, dans la classe d’effet propre à chaque plateforme, est utilisée pour répondre aux changements de la propriété pouvant être liée, comme illustré dans l’exemple de code suivant :

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
      UpdateControl ();
    }
  }
  ...
}
```

La méthode `OnElementPropertyChanged` met à jour le rayon, la couleur ou le décalage de l’ombre si la valeur de la propriété jointe `ShadowEffect` appropriée a changé. Une vérification de la propriété changée doit toujours être effectuée, car cette substitution peut être appelée plusieurs fois.

### <a name="universal-windows-platform-project"></a>Projet de plateforme Windows universelle

L’exemple de code suivant montre l’implémentation de `LabelShadowEffect` pour le projet de plateforme Windows universelle (UWP) :

```csharp
[assembly: ResolutionGroupName ("MyCompany")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        Label shadowLabel;
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;

                    shadowLabel = new Label ();
                    shadowLabel.Text = textBlock.Text;
                    shadowLabel.FontAttributes = FontAttributes.Bold;
                    shadowLabel.HorizontalOptions = LayoutOptions.Center;
                    shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;

                    UpdateColor ();
                    UpdateOffset ();

                    ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                    shadowAdded = true;
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateColor ()
        {
            shadowLabel.TextColor = ShadowEffect.GetColor (Element);
        }

        void UpdateOffset ()
        {
            shadowLabel.TranslationX = ShadowEffect.GetDistanceX (Element);
            shadowLabel.TranslationY = ShadowEffect.GetDistanceY (Element);
        }
    }
}
```

La plateforme Windows universelle ne fournit pas d’effet d’ombre, et l' `LabelShadowEffect` implémentation sur les deux plates-formes en simule une en ajoutant un deuxième décalage [`Label`](xref:Xamarin.Forms.Label) derrière le réplica principal `Label` . La méthode `OnAttached` crée le nouveau contrôle `Label` et définit certaines propriétés de disposition sur `Label`. Il appelle ensuite des méthodes qui récupèrent les valeurs de propriété jointes à l’aide des `ShadowEffect` accesseurs Get, et crée l’ombre en définissant les [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) Propriétés, et [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) pour contrôler la couleur et l’emplacement du `Label` . Le contrôle `shadowLabel` est alors inséré en décalage derrière le contrôle `Label` principal. Cette fonctionnalité est wrappée dans un bloc `try`/`catch` au cas où le contrôle auquel l’effet est joint n’a pas les propriétés `Control.Layer`. Aucune implémentation n’est fournie par la méthode `OnDetached`, car aucun nettoyage n’est nécessaire.

#### <a name="responding-to-property-changes"></a>Répondre aux changements apportés aux propriétés

Si l’une des valeurs de la propriété jointe `ShadowEffect` change au moment de l’exécution, l’effet doit répondre en affichant les changements. Une version substituée de la méthode `OnElementPropertyChanged`, dans la classe d’effet propre à chaque plateforme, est utilisée pour répondre aux changements de la propriété pouvant être liée, comme illustré dans l’exemple de code suivant :

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
                      args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

La méthode `OnElementPropertyChanged` met à jour la couleur ou le décalage de l’ombre si la valeur de la propriété jointe `ShadowEffect` appropriée a changé. Une vérification de la propriété changée doit toujours être effectuée, car cette substitution peut être appelée plusieurs fois.

## <a name="summary"></a>Résumé

Cet article a décrit comment utiliser des propriétés jointes pour passer des paramètres à un effet et comment changer un paramètre au moment de l’exécution. Les propriétés jointes permettent de définir des paramètres d’effet qui répondent aux changements apportés aux propriétés au moment de l’exécution.

## <a name="related-links"></a>Liens associés

- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effet](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Effet d’ombre (exemple)](/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange)