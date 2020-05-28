---
title: ''
description: Les vues natives d’iOS, Android et UWP peuvent être directement référencées à partir des Xamarin.Forms pages créées à l’aide de C#. Cet article explique comment ajouter des vues natives à une Xamarin.Forms mise en page créée à l’aide de C# et comment remplacer la disposition des vues personnalisées pour corriger leur utilisation de l’API de mesure.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 18cdeccbdff86a6b20aab4b33db259f1f06ee096
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139592"
---
# <a name="native-views-in-c"></a>Vues natives en C\#

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)

_Les vues natives d’iOS, Android et UWP peuvent être directement référencées à partir des Xamarin.Forms pages créées à l’aide de C#. Cet article explique comment ajouter des vues natives à une Xamarin.Forms mise en page créée à l’aide de C# et comment remplacer la disposition des vues personnalisées pour corriger leur utilisation de l’API de mesure._

## <a name="overview"></a>Vue d'ensemble

Tout Xamarin.Forms contrôle qui permet `Content` d’être défini ou qui a une `Children` collection peut ajouter des affichages spécifiques à la plateforme. Par exemple, une iOS `UILabel` peut être directement ajoutée à la [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) propriété ou à la [`StackLayout.Children`](xref:Xamarin.Forms.Layout`1.Children) collection. Toutefois, Notez que cette fonctionnalité requiert l’utilisation de `#if` définitions dans les Xamarin.Forms solutions de projet partagées et n’est pas disponible à partir des Xamarin.Forms solutions de bibliothèque .NET standard.

Les captures d’écran suivantes montrent des vues spécifiques à la plateforme qui ont été ajoutées à Xamarin.Forms [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

[![](code-images/screenshots-sml.png "StackLayout Containing Platform-Specific Views")](code-images/screenshots.png#lightbox "StackLayout Containing Platform-Specific Views")

La possibilité d’ajouter des vues spécifiques à une plateforme à une Xamarin.Forms disposition est activée par deux méthodes d’extension sur chaque plateforme :

- `Add`: ajoute une vue spécifique à la plateforme à la [`Children`](xref:Xamarin.Forms.Layout`1.Children) collection d’une disposition.
- `ToView`: prend une vue spécifique à la plateforme et l’encapsule sous la forme d’un Xamarin.Forms [`View`](xref:Xamarin.Forms.View) qui peut être défini en tant que `Content` propriété d’un contrôle.

L’utilisation de ces méthodes dans un Xamarin.Forms projet partagé nécessite l’importation de l’espace de noms approprié spécifique à la plateforme Xamarin.Forms :

- **iOS** – Xamarin.Forms . Platform. iOS
- **Android** – Xamarin.Forms . Platform. Android
- **Plateforme Windows universelle (UWP)** : Xamarin.Forms . Platform. UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Ajout d’affichages spécifiques à la plateforme sur chaque plateforme

Les sections suivantes montrent comment ajouter des affichages spécifiques à la plateforme à une Xamarin.Forms disposition sur chaque plateforme.

### <a name="ios"></a>iOS

L’exemple de code suivant montre comment ajouter un `UILabel` à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) et un [`ContentView`](xref:Xamarin.Forms.ContentView) :

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

L’exemple suppose que les `stackLayout` instances et `contentView` ont été créées précédemment en XAML ou C#.

### <a name="android"></a>Téléphone

L’exemple de code suivant montre comment ajouter un `TextView` à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) et un [`ContentView`](xref:Xamarin.Forms.ContentView) :

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

L’exemple suppose que les `stackLayout` instances et `contentView` ont été créées précédemment en XAML ou C#.

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

L’exemple de code suivant montre comment ajouter un `TextBlock` à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) et un [`ContentView`](xref:Xamarin.Forms.ContentView) :

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

L’exemple suppose que les `stackLayout` instances et `contentView` ont été créées précédemment en XAML ou C#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Substitution des mesures de la plateforme pour les vues personnalisées

Les vues personnalisées sur chaque plateforme implémentent souvent uniquement des mesures uniquement pour le scénario de disposition pour lequel elles ont été conçues. Par exemple, un affichage personnalisé peut avoir été conçu pour occuper uniquement la moitié de la largeur disponible de l’appareil. Toutefois, une fois partagée avec d’autres utilisateurs, la vue personnalisée peut être nécessaire pour occuper toute la largeur disponible de l’appareil. Par conséquent, il peut être nécessaire de remplacer une implémentation de mesure des vues personnalisées lors de sa réutilisation dans une Xamarin.Forms disposition. Pour cette raison, les `Add` `ToView` méthodes d’extension et fournissent des substitutions qui permettent de spécifier des délégués de mesure, qui peuvent substituer la disposition de vue personnalisée quand elle est ajoutée à une Xamarin.Forms disposition.

Les sections suivantes montrent comment remplacer la disposition des vues personnalisées pour corriger leur utilisation de l’API de mesure.

### <a name="ios"></a>iOS

L’exemple de code suivant montre la `CustomControl` classe qui hérite de `UILabel` :

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

Une instance de cette vue est ajoutée à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , comme illustré dans l’exemple de code suivant :

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

Toutefois, étant donné que le `CustomControl.SizeThatFits` remplacement retourne toujours une hauteur de 150, la vue sera affichée avec un espace vide au-dessus et au-dessous de celle-ci, comme illustré dans la capture d’écran suivante :

![](code-images/ios-bad-measurement.png "iOS CustomControl with Bad SizeThatFits Implementation")

Une solution à ce problème consiste à fournir une `GetDesiredSizeDelegate` implémentation, comme illustré dans l’exemple de code suivant :

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

Cette méthode utilise la largeur fournie par la `CustomControl.SizeThatFits` méthode, mais remplace la hauteur de 150 pour une hauteur de 70. Lorsque l' `CustomControl` instance est ajoutée au [`StackLayout`](xref:Xamarin.Forms.StackLayout) , la `FixSize` méthode peut être spécifiée en tant que `GetDesiredSizeDelegate` pour corriger la mauvaise mesure fournie par la `CustomControl` classe :

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Cela entraîne l’affichage correct de la vue personnalisée, sans espace vide au-dessus et au-dessous de celle-ci, comme illustré dans la capture d’écran suivante :

![](code-images/ios-good-measurement.png "iOS CustomControl with GetDesiredSize Override")

### <a name="android"></a>Téléphone

L’exemple de code suivant montre la `CustomControl` classe qui hérite de `TextView` :

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

Une instance de cette vue est ajoutée à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , comme illustré dans l’exemple de code suivant :

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

Toutefois, étant donné que le `CustomControl.OnMeasure` remplacement retourne toujours la moitié de la largeur demandée, la vue sera affichée en occupant uniquement la moitié de la largeur disponible de l’appareil, comme illustré dans la capture d’écran suivante :

![](code-images/android-bad-measurement.png "Android CustomControl with Bad OnMeasure Implementation")

Une solution à ce problème consiste à fournir une `GetDesiredSizeDelegate` implémentation, comme illustré dans l’exemple de code suivant :

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

Cette méthode utilise la largeur fournie par la `CustomControl.OnMeasure` méthode, mais la multiplie par deux. Lorsque l' `CustomControl` instance est ajoutée au [`StackLayout`](xref:Xamarin.Forms.StackLayout) , la `FixSize` méthode peut être spécifiée en tant que `GetDesiredSizeDelegate` pour corriger la mauvaise mesure fournie par la `CustomControl` classe :

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Cela entraîne l’affichage correct de la vue personnalisée, en occupant la largeur de l’appareil, comme illustré dans la capture d’écran suivante :

![](code-images/android-good-measurement.png "Android CustomControl with Custom GetDesiredSize Delegate")

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

L’exemple de code suivant montre la `CustomControl` classe qui hérite de `Panel` :

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

Une instance de cette vue est ajoutée à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , comme illustré dans l’exemple de code suivant :

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

Toutefois, étant donné que le `CustomControl.ArrangeOverride` remplacement retourne toujours la moitié de la largeur demandée, la vue sera découpée à la moitié de la largeur disponible de l’appareil, comme illustré dans la capture d’écran suivante :

![](code-images/winrt-bad-measurement.png "UWP CustomControl with Bad ArrangeOverride Implementation")

Une solution à ce problème consiste à fournir une `ArrangeOverrideDelegate` implémentation, lors de l’ajout de la vue à [`StackLayout`](xref:Xamarin.Forms.StackLayout) , comme illustré dans l’exemple de code suivant :

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

Cette méthode utilise la largeur fournie par la `CustomControl.ArrangeOverride` méthode, mais la multiplie par deux. Cela entraîne l’affichage correct de la vue personnalisée, en occupant la largeur de l’appareil, comme illustré dans la capture d’écran suivante :

![](code-images/winrt-good-measurement.png "UWP CustomControl with ArrangeOverride Delegate")

## <a name="summary"></a>Résumé

Cet article a expliqué comment ajouter des vues natives à une Xamarin.Forms mise en page créée à l’aide de C# et comment remplacer la disposition des vues personnalisées pour corriger leur utilisation de l’API de mesure.

## <a name="related-links"></a>Liens connexes

- [NativeEmbedding (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)
- [Formulaires natifs](~/xamarin-forms/platform/native-forms.md)
