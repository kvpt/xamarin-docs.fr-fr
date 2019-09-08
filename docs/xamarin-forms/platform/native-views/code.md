---
title: Vues natives en c#
description: Vues natives à partir d’iOS, Android et UWP peuvent être référencées directement à partir des pages Xamarin.Forms créées à l’aide de c#. Cet article montre comment ajouter des vues natives à une disposition de Xamarin.Forms créée à l’aide de c# et comment remplacer la disposition des vues personnalisées pour corriger leur utilisation de l’API de mesure.
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: a759569f9faf7c8e13768a68b203a8221d3b31cb
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770712"
---
# <a name="native-views-in-c"></a>Vues natives en C\#

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)

_Vues natives à partir d’iOS, Android et UWP peuvent être référencées directement à partir des pages Xamarin.Forms créées à l’aide de c#. Cet article montre comment ajouter des vues natives à une disposition de Xamarin.Forms créée à l’aide de c# et comment remplacer la disposition des vues personnalisées pour corriger leur utilisation de l’API de mesure._

## <a name="overview"></a>Vue d'ensemble

N’importe quel contrôle Xamarin.Forms qui permet de `Content` à définir, ou qui a un `Children` collection, peut ajouter des vues spécifiques à la plateforme. Par exemple, un iOS `UILabel` peuvent être ajoutés directement à la [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) propriété, ou vers le [ `StackLayout.Children` ](xref:Xamarin.Forms.Layout`1.Children) collection. Toutefois, notez que cette fonctionnalité nécessite l’utilisation de `#if` définit dans les solutions Xamarin.Forms partagé projet et n’est pas disponible à partir des solutions de bibliothèque Xamarin.Forms .NET Standard.

Les captures d’écran suivantes illustrent spécifiques à la plateforme vues ayant été ajouté à un Xamarin.Forms [ `StackLayout` ](xref:Xamarin.Forms.StackLayout):

[![](code-images/screenshots-sml.png "StackLayout contenant des vues de spécifique à la plateforme")](code-images/screenshots.png#lightbox "StackLayout contenant des vues de spécifique à la plateforme")

La possibilité d’ajouter des vues spécifiques à la plateforme à une disposition de Xamarin.Forms est activée par deux méthodes d’extension sur chaque plateforme :

- `Add` : ajoute une vue spécifique à la plateforme pour le [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) collection d’une disposition.
- `ToView` – prend une vue spécifique à la plateforme et l’enveloppe en tant qu’un Xamarin.Forms [ `View` ](xref:Xamarin.Forms.View) qui peut être défini comme le `Content` propriété d’un contrôle.

À l’aide de ces méthodes dans un projet partagé Xamarin.Forms nécessite l’importation de l’espace de noms de Xamarin.Forms spécifique à la plateforme appropriée :

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Plateforme Windows universelle (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Ajout de vues spécifiques à la plateforme sur chaque plateforme

Les sections suivantes expliquent comment ajouter des vues spécifiques à la plateforme à une disposition de Xamarin.Forms sur chaque plateforme.

### <a name="ios"></a>iOS

L’exemple de code suivant montre comment ajouter un `UILabel` à un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) et un [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var uiLabel = new UILabel {
  MinimumFontSize = 14f,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = originalText,
};
stackLayout.Children.Add (uiLabel);
contentView.Content = uiLabel.ToView();
```

L’exemple suppose que le `stackLayout` et `contentView` instances précédemment créées en XAML ou c#.

### <a name="android"></a>Android

L’exemple de code suivant montre comment ajouter un `TextView` à un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) et un [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

L’exemple suppose que le `stackLayout` et `contentView` instances précédemment créées en XAML ou c#.

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

L’exemple de code suivant montre comment ajouter un `TextBlock` à un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) et un [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var textBlock = new TextBlock
{
    Text = originalText,
    FontSize = 14,
    FontFamily = new FontFamily("HelveticaNeue"),
    TextWrapping = TextWrapping.Wrap
};
stackLayout.Children.Add(textBlock);
contentView.Content = textBlock.ToView();
```

L’exemple suppose que le `stackLayout` et `contentView` instances précédemment créées en XAML ou c#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Substitution de mesures de plate-forme pour des vues personnalisées

Vues personnalisées sur chaque plateforme implémentent souvent uniquement correctement mesure pour le scénario de mise en page pour laquelle ils ont été conçus. Par exemple, une vue personnalisée peut ont été conçue uniquement occupe la moitié de la largeur disponible de l’appareil. Toutefois, après avoir partagé avec d’autres utilisateurs, la vue personnalisée peut être requise pour occuper la largeur totale disponible de l’appareil. Par conséquent, il peut être nécessaire de remplacer une implémentation de mesure des vues personnalisées lors de la réutilisation dans une disposition de Xamarin.Forms. Pour cette raison, le `Add` et `ToView` les méthodes d’extension fournissent les substitutions qui permettent aux délégués de mesure de la définir, qui peuvent remplacer la disposition de la vue personnalisée lorsqu’il est ajouté à une disposition de Xamarin.Forms.

Les sections suivantes montrent comment remplacer la disposition des vues personnalisées, à corriger leur utilisation de l’API de mesure.

### <a name="ios"></a>iOS

Le code suivant montre l’exemple le `CustomControl` (classe), qui hérite de `UILabel`:

```csharp
public class CustomControl : UILabel
{
  public override string Text {
    get { return base.Text; }
    set { base.Text = value.ToUpper (); }
  }

  public override CGSize SizeThatFits (CGSize size)
  {
    return new CGSize (size.Width, 150);
  }
}
```

Une instance de cette vue est ajoutée à un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), comme illustré dans l’exemple de code suivant :

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

Toutefois, étant donné que le `CustomControl.SizeThatFits` substitution retourne toujours une hauteur de 150, la vue s’affiche avec un espace vide au-dessus et en dessous, comme indiqué dans la capture d’écran suivante :

![](code-images/ios-bad-measurement.png "iOS CustomControl avec une mauvaise implémentation SizeThatFits")

Une solution à ce problème consiste à fournir un `GetDesiredSizeDelegate` implémentation, comme illustré dans l’exemple de code suivant :

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, double width, double height)
{
  var uiView = renderer.Control;

  if (uiView == null) {
    return null;
  }

  var constraint = new CGSize (width, height);

  // Let the CustomControl determine its size (which will be wrong)
  var badRect = uiView.SizeThatFits (constraint);

  // Use the width and substitute the height
  return new SizeRequest (new Size (badRect.Width, 70));
}
```

Cette méthode utilise la largeur fournie par le `CustomControl.SizeThatFits` (méthode), mais la remplace par la hauteur de 150 pour une hauteur de 70. Lorsque le `CustomControl` instance est ajoutée à la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), le `FixSize` méthode peut être spécifiée en tant que le `GetDesiredSizeDelegate` pour corriger la mesure incorrecte fournie par le `CustomControl` classe :

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Il en résulte dans la vue personnalisée qui est affichée correctement, sans espace vide au-dessus et au-dessous d’elle, comme illustré dans la capture d’écran suivante :

![](code-images/ios-good-measurement.png "iOS CustomControl avec GetDesiredSize remplacement")

### <a name="android"></a>Android

Le code suivant montre l’exemple le `CustomControl` (classe), qui hérite de `TextView`:

```csharp
public class CustomControl : TextView
{
  public CustomControl (Context context) : base (context)
  {
  }

  protected override void OnMeasure (int widthMeasureSpec, int heightMeasureSpec)
  {
    int width = MeasureSpec.GetSize (widthMeasureSpec);

    // Force the width to half of what's been requested.
    // This is deliberately wrong to demonstrate providing an override to fix it with.
    int widthSpec = MeasureSpec.MakeMeasureSpec (width / 2, MeasureSpec.GetMode (widthMeasureSpec));

    base.OnMeasure (widthSpec, heightMeasureSpec);
  }
}
```

Une instance de cette vue est ajoutée à un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), comme illustré dans l’exemple de code suivant :

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

Toutefois, étant donné que le `CustomControl.OnMeasure` substitution retourne toujours la moitié de la largeur demandée, la vue s’affichera occupant seulement la moitié de la largeur disponible de l’appareil, comme illustré dans la capture d’écran suivante :

![](code-images/android-bad-measurement.png "Android CustomControl avec implémentation de OnMeasure incorrecte")

Une solution à ce problème consiste à fournir un `GetDesiredSizeDelegate` implémentation, comme illustré dans l’exemple de code suivant :

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, int widthConstraint, int heightConstraint)
{
  var nativeView = renderer.Control;

  if ((widthConstraint == 0 && heightConstraint == 0) || nativeView == null) {
    return null;
  }

  int width = Android.Views.View.MeasureSpec.GetSize (widthConstraint);
  int widthSpec = Android.Views.View.MeasureSpec.MakeMeasureSpec (
    width * 2, Android.Views.View.MeasureSpec.GetMode (widthConstraint));
  nativeView.Measure (widthSpec, heightConstraint);
  return new SizeRequest (new Size (nativeView.MeasuredWidth, nativeView.MeasuredHeight));
}
```

Cette méthode utilise la largeur fournie par le `CustomControl.OnMeasure` (méthode), mais il multiplie par deux. Lorsque le `CustomControl` instance est ajoutée à la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), le `FixSize` méthode peut être spécifiée en tant que le `GetDesiredSizeDelegate` pour corriger la mesure incorrecte fournie par le `CustomControl` classe :

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Il en résulte dans la vue personnalisée en cours s’affiche correctement, qui occupent la largeur de l’appareil, comme illustré dans la capture d’écran suivante :

![](code-images/android-good-measurement.png "CustomControl Android avec le délégué de GetDesiredSize personnalisé")

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Le code suivant montre l’exemple le `CustomControl` (classe), qui hérite de `Panel`:

```csharp
public class CustomControl : Panel
{
  public static readonly DependencyProperty TextProperty =
    DependencyProperty.Register(
      "Text", typeof(string), typeof(CustomControl), new PropertyMetadata(default(string), OnTextPropertyChanged));

  public string Text
  {
    get { return (string)GetValue(TextProperty); }
    set { SetValue(TextProperty, value.ToUpper()); }
  }

  readonly TextBlock textBlock;

  public CustomControl()
  {
    textBlock = new TextBlock
    {
      MinHeight = 0,
      MaxHeight = double.PositiveInfinity,
      MinWidth = 0,
      MaxWidth = double.PositiveInfinity,
      FontSize = 14,
      TextWrapping = TextWrapping.Wrap,
      VerticalAlignment = VerticalAlignment.Center
    };

    Children.Add(textBlock);
  }

  static void OnTextPropertyChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
  {
    ((CustomControl)dependencyObject).textBlock.Text = (string)args.NewValue;
  }

  protected override Size ArrangeOverride(Size finalSize)
  {
      // This is deliberately wrong to demonstrate providing an override to fix it with.
      textBlock.Arrange(new Rect(0, 0, finalSize.Width/2, finalSize.Height));
      return finalSize;
  }

  protected override Size MeasureOverride(Size availableSize)
  {
      textBlock.Measure(availableSize);
      return new Size(textBlock.DesiredSize.Width, textBlock.DesiredSize.Height);
  }
}
```

Une instance de cette vue est ajoutée à un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), comme illustré dans l’exemple de code suivant :

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

Toutefois, étant donné que le `CustomControl.ArrangeOverride` substitution retourne toujours la moitié de la largeur demandée, la vue est découpée selon la moitié de la largeur disponible de l’appareil, comme illustré dans la capture d’écran suivante :

![](code-images/winrt-bad-measurement.png "CustomControl UWP avec implémentation de ArrangeOverride incorrecte")

Une solution à ce problème consiste à fournir un `ArrangeOverrideDelegate` implémentation, lors de l’ajout de la vue à la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), comme illustré dans l’exemple de code suivant :

```csharp
stackLayout.Children.Add(fixedControl, arrangeOverrideDelegate: (renderer, finalSize) =>
{
    if (finalSize.Width <= 0 || double.IsInfinity(finalSize.Width))
    {
        return null;
    }
    var frameworkElement = renderer.Control;
    frameworkElement.Arrange(new Rect(0, 0, finalSize.Width * 2, finalSize.Height));
    return finalSize;
});
```

Cette méthode utilise la largeur fournie par le `CustomControl.ArrangeOverride` (méthode), mais il multiplie par deux. Il en résulte dans la vue personnalisée en cours s’affiche correctement, qui occupent la largeur de l’appareil, comme illustré dans la capture d’écran suivante :

![](code-images/winrt-good-measurement.png "CustomControl UWP avec ArrangeOverride délégué")

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment ajouter des vues natives à une disposition de Xamarin.Forms créée à l’aide de c# et comment remplacer la disposition des vues personnalisées pour corriger leur utilisation de l’API de mesure.

## <a name="related-links"></a>Liens associés

- [NativeEmbedding (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)
- [Formulaires natifs](~/xamarin-forms/platform/native-forms.md)
