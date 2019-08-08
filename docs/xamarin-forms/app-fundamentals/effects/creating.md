---
title: Création d’un effet
description: Les effets simplifient la personnalisation d’un contrôle. Cet article montre comment créer un effet qui change la couleur d’arrière-plan du contrôle Entry quand le contrôle obtient le focus.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: f1c18c30a2da019fb9d1c09fac17c9f095dafedc
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739343"
---
# <a name="creating-an-effect"></a>Création d’un effet

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)

_Les effets simplifient la personnalisation d’un contrôle. Cet article montre comment créer un effet qui change la couleur d’arrière-plan du contrôle Entry quand le contrôle obtient le focus._

Le processus de création d’un effet dans chaque projet spécifique à une plateforme est le suivant :

1. Créez une sous-classe de la classe `PlatformEffect`.
1. Remplacez la méthode `OnAttached` et écrivez une logique pour personnaliser le contrôle.
1. Remplacez la méthode `OnDetached` et écrivez si nécessaire une logique pour nettoyer la personnalisation du contrôle.
1. Ajoutez un attribut [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) à la classe de l’effet. Cet attribut définit un espace de noms à l’échelle de l’entreprise pour les effets, empêchant des collisions avec d’autres effets du même nom. Notez que cet attribut peut être appliqué seulement une fois par projet.
1. Ajoutez un attribut [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) à la classe de l’effet. Cet attribut inscrit l’effet avec un ID unique utilisé par Xamarin.Forms ainsi que le nom du groupe, pour localiser l’effet avant de l’appliquer à un contrôle. L’attribut prend deux paramètres : le nom de type de l’effet et une chaîne unique qui est utilisée pour localiser l’effet avant de l’appliquer à un contrôle.

L’effet peut ensuite être consommé en l’attachant au contrôle approprié.

> [!NOTE]
> Fournir un effet dans chaque projet de plateforme est facultatif. Une tentative d’utiliser un effet quand il n’est pas inscrit retourne une valeur non null qui ne fait rien.

L’exemple d’application montre un `FocusEffect` qui change la couleur d’arrière-plan d’un contrôle quand il obtient le focus. Le diagramme suivant montre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](creating-images/focus-effect.png "Responsabilités des projets d’effet de focus")

Un contrôle [`Entry`](xref:Xamarin.Forms.Entry) sur la page `HomePage` est personnalisé par la classe `FocusEffect` dans chaque projet spécifique à une plateforme. Chaque classe `FocusEffect` dérive de la classe `PlatformEffect` pour chaque plateforme. Il en résulte un rendu du contrôle `Entry` avec une couleur d’arrière-plan spécifique à la plateforme, qui change quand le contrôle obtient le focus, comme le montrent les captures d’écran suivantes :

![](creating-images/screenshots-1.png "Effet de focus sur chaque plateforme")
![](creating-images/screenshots-2.png "Effet de focus sur chaque plateforme")

## <a name="creating-the-effect-on-each-platform"></a>Création de l’effet sur chaque plateforme

Les sections suivantes décrivent l’implémentation de la classe `FocusEffect` pour chaque plateforme.

## <a name="ios-project"></a>Projet iOS

L’exemple de code suivant illustre l’implémentation de `FocusEffect` pour le projet iOS :

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(EffectsDemo.iOS.FocusEffect), nameof(EffectsDemo.iOS.FocusEffect))]
namespace EffectsDemo.iOS
{
    public class FocusEffect : PlatformEffect
    {
        UIColor backgroundColor;

        protected override void OnAttached ()
        {
            try {
                Control.BackgroundColor = backgroundColor = UIColor.FromRGB (204, 153, 255);
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);

            try {
                if (args.PropertyName == "IsFocused") {
                    if (Control.BackgroundColor == backgroundColor) {
                        Control.BackgroundColor = UIColor.White;
                    } else {
                        Control.BackgroundColor = backgroundColor;
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

La méthode `OnAttached` définit la propriété `BackgroundColor` sur la couleur « light purple » (violet clair) avec la méthode `UIColor.FromRGB` et stocke également cette couleur dans un champ. Cette fonctionnalité est wrappée dans un bloc `try`/`catch`, au cas où le contrôle auquel l’effet est attaché n’aurait pas de propriété `BackgroundColor`. Aucune implémentation n’est fournie par la méthode `OnDetached`, car aucun nettoyage n’est nécessaire.

Le remplacement de `OnElementPropertyChanged` répond aux modifications des propriétés pouvant être liées sur le contrôle Xamarin.Forms. Quand la propriété [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) change, la propriété `BackgroundColor` du contrôle est changée en « white » (blanc) si le contrôle a le focus, sinon il est changé en « light purple » (violet clair). Cette fonctionnalité est wrappée dans un bloc `try`/`catch`, au cas où le contrôle auquel l’effet est attaché n’aurait pas de propriété `BackgroundColor`.

## <a name="android-project"></a>Projet Android

L’exemple de code suivant illustre l’implémentation de `FocusEffect` pour le projet Android :

```csharp
using System;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.Droid.FocusEffect), nameof(EffectsDemo.Droid.FocusEffect))]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color originalBackgroundColor = new Android.Graphics.Color(0, 0, 0, 0);
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached()
        {
            try
            {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor(backgroundColor);
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);
            try
            {
                if (args.PropertyName == "IsFocused")
                {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor)
                    {
                        Control.SetBackgroundColor(originalBackgroundColor);
                    }
                    else
                    {
                        Control.SetBackgroundColor(backgroundColor);
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

La méthode `OnAttached` appelle la méthode `SetBackgroundColor` pour définir la couleur d’arrière-plan du contrôle en vert clair et stocke également cette couleur dans un champ. Cette fonctionnalité est wrappée dans un bloc `try`/`catch`, au cas où le contrôle auquel l’effet est attaché n’aurait pas de propriété `SetBackgroundColor`. Aucune implémentation n’est fournie par la méthode `OnDetached`, car aucun nettoyage n’est nécessaire.

Le remplacement de `OnElementPropertyChanged` répond aux modifications des propriétés pouvant être liées sur le contrôle Xamarin.Forms. Quand la propriété [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) change, la couleur d’arrière-plan du contrôle est changée en « white » (blanc) si le contrôle a le focus, sinon il est changé en « light green » (vert clair). Cette fonctionnalité est wrappée dans un bloc `try`/`catch`, au cas où le contrôle auquel l’effet est attaché n’aurait pas de propriété `BackgroundColor`.

## <a name="universal-windows-platform-projects"></a>Projets UWP (Plateforme Windows universelle)

L’exemple de code suivant montre l’implémentation de `FocusEffect` pour le projet de plateforme UWP :

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.UWP.FocusEffect), nameof(EffectsDemo.UWP.FocusEffect))]
namespace EffectsDemo.UWP
{
    public class FocusEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            try
            {
                (Control as Windows.UI.Xaml.Controls.Control).Background = new SolidColorBrush(Colors.Cyan);
                (Control as FormsTextBox).BackgroundFocusBrush = new SolidColorBrush(Colors.White);
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }
    }
}
```

La méthode `OnAttached` définit la propriété `Background` du contrôle sur « cyan » et définit la propriété `BackgroundFocusBrush` sur « white » (blanc). Cette fonctionnalité est wrappée dans un bloc `try`/`catch`, au cas où le contrôle auquel l’effet est attaché n’aurait pas ces propriétés. Aucune implémentation n’est fournie par la méthode `OnDetached`, car aucun nettoyage n’est nécessaire.

## <a name="consuming-the-effect"></a>Consommation de l’effet

Le processus de consommation d’un effet provenant d’une bibliothèque Xamarin.Forms .NET Standard ou d’un projet de bibliothèque partagée est le suivant :

1. Déclarez un contrôle qui sera personnalisé par l’effet.
1. Attachez l’effet au contrôle en l’ajoutant à la collection [`Effects`](xref:Xamarin.Forms.Element.Effects) du contrôle.

> [!NOTE]
> Une instance de l’effet ne peut être attachée qu’à un seul contrôle. Par conséquent, un effet doit être résolu deux fois pour être utilisé sur deux contrôles.

## <a name="consuming-the-effect-in-xaml"></a>Consommation de l’effet en XAML

L’exemple de code XAML suivant montre un contrôle [`Entry`](xref:Xamarin.Forms.Entry) auquel `FocusEffect` est attaché :

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

La classe `FocusEffect` de la bibliothèque .NET Standard prend en charge la consommation d’effets en XAML et elle apparaît dans l’exemple de code suivant :

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ($"MyCompany.{nameof(FocusEffect)}")
    {
    }
}
```

La classe `FocusEffect` crée une sous-classe de la classe [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect), qui représente un effet indépendant de la plateforme qui wrappe un effet interne généralement spécifique à une plateforme. La classe `FocusEffect` appelle le constructeur de classe de base, en passant un paramètre constitué d’une concaténation du nom du groupe de résolution (spécifié avec l’attribut [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) sur la classe d’effet), et l’ID unique qui a été spécifié avec l’attribut [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) sur la classe d’effet. Par conséquent, quand [`Entry`](xref:Xamarin.Forms.Entry) est initialisé à l’exécution, une nouvelle instance de `MyCompany.FocusEffect` est ajoutée à la collection [`Effects`](xref:Xamarin.Forms.Element.Effects) du contrôle.

Les effets peuvent également être attachés à des contrôles avec un comportement ou avec des propriétés attachées. Pour plus d’informations sur l’attachement d’un effet à un contrôle avec un comportement, consultez [Comportement d’effet réutilisable](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md). Pour plus d’informations sur l’attachement d’un effet à un contrôle avec des propriétés attachées, consultez [Passage de paramètres à un effet](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md).

## <a name="consuming-the-effect-in-cnum"></a>Consommation de l’effet en C&num;

Le contrôle [`Entry`](xref:Xamarin.Forms.Entry) équivalent en C# est présenté dans l’exemple de code suivant :

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

`FocusEffect` est attaché à la l’instance de `Entry` en ajoutant l’effet à la collection [`Effects`](xref:Xamarin.Forms.Element.Effects) du contrôle, comme illustré dans l’exemple de code suivant :

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ($"MyCompany.{nameof(FocusEffect)}"));
  ...
}
```

[`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) retourne un [`Effect`](xref:Xamarin.Forms.Effect) pour le nom spécifié, qui est une concaténation du nom du groupe de résolution (spécifié avec l’attribut [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) sur la classe d’effet), et l’ID unique qui a été spécifié avec l’attribut [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) sur la classe d’effet. Si une plateforme ne fournit pas l’effet, la méthode `Effect.Resolve` retourne une valeur non `null`.

## <a name="summary"></a>Récapitulatif

Cet article montre comment créer un effet qui change la couleur d’arrière-plan du contrôle [`Entry`](xref:Xamarin.Forms.Entry) quand le contrôle obtient le focus.


## <a name="related-links"></a>Liens associés

- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Effet de couleur d’arrière-plan (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-backgroundcoloreffect)
- [Effet de focus (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)
