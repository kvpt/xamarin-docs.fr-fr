---
title: Appel d’événements à partir d’effets
description: Un effet peut définir et appeler un événement, en signalant des changements dans la vue native sous-jacente. Cet article explique comment implémenter le suivi de l’interaction tactile multipoint de bas niveau et comment générer des événements qui indiquent une activité tactile.
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: cf5a97bca7c827101db951a440863839539c7e48
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725258"
---
# <a name="invoking-events-from-effects"></a>Appel d’événements à partir d’effets

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)

_Un effet peut définir et invoquer un événement, signalant des changements dans la vue autochtone sous-jacente. Cet article montre comment implémenter le suivi multi-touch de bas niveau des doigts, et comment générer des événements qui signalent l’activité tactile._

L’effet décrit dans cet article fournit l’accès aux événements tactiles de bas niveau. Ces événements de bas niveau ne sont pas disponibles via les classes `GestureRecognizer` existantes, mais ils sont très importants pour certains types d’applications. Par exemple, une application de peinture tactile a besoin de suivre les doigts individuels au fil de leur déplacement sur l’écran. Un clavier musical doit détecter les appuis et les relâchements sur les touches individuelles, mais aussi un doigt glissant d’une touche à l’autre dans un glissando.

Un effet est idéal pour le suivi de l’interaction tactile multipoint, car il peut être attaché à n’importe quel élément de Xamarin.Forms.

## <a name="platform-touch-events"></a>Événements tactiles des plateformes

Les plateformes iOS, Android et Windows universelle incluent toutes une API de bas niveau qui permet aux applications de détecter l’activité tactile. Ces plateformes distinguent toutes trois types d’événements tactiles de base :

- *Enfoncé*, quand un doigt touche l’écran
- *Déplacé*, quand un doigt touchant l’écran se déplace
- *Relâché*, quand le doigt est détaché de l’écran

Dans un environnement d’interaction tactile multipoint, plusieurs doigts peuvent toucher l’écran en même temps. Les différentes plateformes incluent un numéro d’identification (ID) que les applications peuvent utiliser pour faire la distinction entre plusieurs doigts.

Dans iOS, la classe `UIView` définit trois méthodes remplaçables, `TouchesBegan`, `TouchesMoved`, et `TouchesEnded`, correspondant à ces trois événements de base. L’article [Suivi de l’interaction tactile multipoint](~/ios/app-fundamentals/touch/touch-tracking.md) explique comment utiliser ces méthodes. Cependant, un programme iOS n’a pas besoin de remplacer une classe qui dérive de `UIView` pour utiliser ces méthodes. Dans iOS, `UIGestureRecognizer` définit également ces trois mêmes méthodes, et vous pouvez attacher une instance d’une classe qui dérive de `UIGestureRecognizer` à n’importe quel objet `UIView`.

Dans Android, la classe `View` définit une méthode remplaçable nommée `OnTouchEvent` pour traiter toutes les activités tactiles. Le type de l’activité tactile est défini par les membres d’énumération `Down`, `PointerDown`, `Move`, `Up` et `PointerUp`, comme décrit dans l’article [Suivi de l’interaction tactile multipoint](~/android/app-fundamentals/touch/touch-tracking.md). Dans Android, `View` définit également un événement nommé `Touch`, qui permet d’attacher un gestionnaire d’événements à n’importe quel objet `View`.

Sur la plateforme Windows universelle (UWP), la classe `UIElement` définit des événements nommés `PointerPressed`, `PointerMoved` et `PointerReleased`. Ceux-ci sont décrits dans l’article [Gérer une entrée de pointeur sur MSDN](/windows/uwp/input-and-devices/handle-pointer-input/) et dans la documentation de l’API pour la classe [`UIElement`](/uwp/api/windows.ui.xaml.uielement/).

L’API `Pointer` de la plateforme Windows universelle est destinée à unifier les entrées de la souris, de l’écran tactile et du stylet. Pour cette raison, l’événement `PointerMoved` est appelé quand la souris traverse un élément, même si l’utilisateur n’appuie pas sur un bouton de la souris. L’objet `PointerRoutedEventArgs` qui accompagne ces événements a une propriété nommée `Pointer`, qui a une propriété nommée `IsInContact` indiquant si un bouton de la souris est enfoncé ou si un doigt est en contact avec l’écran.

En outre, la plateforme Windows universelle définit deux autres événements nommés `PointerEntered` et `PointerExited`. Ils indiquent quand une souris ou un doigt se déplace d’un élément à un autre. Par exemple, considérez les deux éléments adjacents nommés A et B. Les deux éléments ont des gestionnaires installés pour les événements de pointeur. Quand un doigt appuie sur A, l’événement `PointerPressed` est appelé. Quand le doigt se déplace, A appelle les événements `PointerMoved`. Si le doigt se déplace de A à B, A appelle un événement `PointerExited` et B appelle un événement `PointerEntered`. Si le doigt est ensuite détaché de l’écran, B appelle un événement `PointerReleased`.

Les plateformes iOS et Android sont différentes de la plateforme Windows universelle : la vue qui reçoit d’abord l’appel à `TouchesBegan` ou `OnTouchEvent` quand un doigt touche la vue continue de recevoir toutes les activités tactiles, même si le doigt se déplace vers d’autres vues. La plateforme Windows universelle peut se comporter de façon similaire si l’application capture le pointeur : dans le gestionnaire d’événements `PointerEntered`, l’élément appelle `CapturePointer` et obtient ensuite toute l’activité tactile de ce doigt.

L’approche de la plateforme Windows universelle s’avère très pratique pour certains types d’applications, par exemple un clavier musical. Chaque touche peut gérer les événements tactiles pour cette touche, et détecter quand un doigt a glissé d’une touche à une autre en utilisant les événements `PointerEntered` et `PointerExited`.

Pour cette raison, l’effet de suivi tactile décrit dans cet article implémente l’approche UWP.

## <a name="the-touch-tracking-effect-api"></a>L’API Effet de suivi tactile

L’exemple [**Touch Tracking Effect Demos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) contient les classes (et une énumération) qui implémentent le suivi tactile de bas niveau. Ces types appartiennent à l’espace de noms `TouchTracking` et commencent par le mot `Touch`. Le projet de bibliothèque .NET Standard **TouchTrackingEffectDemos** inclut l’énumération `TouchActionType` pour le type d’événements tactiles :

```csharp
public enum TouchActionType
{
    Entered,
    Pressed,
    Moved,
    Released,
    Exited,
    Cancelled
}
```

Toutes les plateformes incluent également un événement qui indique que l’événement tactile a été annulé.

La classe `TouchEffect` de la bibliothèque .NET Standard dérive de `RoutingEffect`, et définit un événement nommé `TouchAction` et une méthode nommée `OnTouchAction` qui appelle l’événement `TouchAction` :

```csharp
public class TouchEffect : RoutingEffect
{
    public event TouchActionEventHandler TouchAction;

    public TouchEffect() : base("XamarinDocs.TouchEffect")
    {
    }

    public bool Capture { set; get; }

    public void OnTouchAction(Element element, TouchActionEventArgs args)
    {
        TouchAction?.Invoke(element, args);
    }
}
```

Notez également la propriété `Capture`. Pour capturer les événements tactiles, une application doit définir cette propriété sur `true` avant un événement `Pressed`. Sinon, les événements tactiles se comportent comme ceux de la plateforme Windows universelle.

La classe `TouchActionEventArgs` de la bibliothèque .NET Standard contient toutes les informations qui accompagnent chaque événement :

```csharp
public class TouchActionEventArgs : EventArgs
{
    public TouchActionEventArgs(long id, TouchActionType type, Point location, bool isInContact)
    {
        Id = id;
        Type = type;
        Location = location;
        IsInContact = isInContact;
    }

    public long Id { private set; get; }

    public TouchActionType Type { private set; get; }

    public Point Location { private set; get; }

    public bool IsInContact { private set; get; }
}
```

Une application peut utiliser la propriété `Id` pour suivre des doigts individuels. Notez la propriété `IsInContact`. Cette propriété est toujours `true` pour les événements `Pressed` et `false` pour les événements `Released`. Elle est également toujours `true` pour les événements `Moved` sur iOS et Android. La propriété `IsInContact` peut être `false` pour les événements `Moved` sur la plateforme Windows universelle quand le programme est en cours d’exécution sur le poste de travail et que le pointeur de la souris se déplace sans qu’un bouton soit enfoncé.

Vous pouvez utiliser la classe `TouchEffect` dans vos propres applications, en incluant le fichier dans le projet de bibliothèque .NET Standard de la solution et en ajoutant une instance de la collection `Effects` de n’importe quel élément de Xamarin.Forms. Attachez un gestionnaire à l’événement `TouchAction` pour obtenir les événements tactiles.

Pour utiliser `TouchEffect` dans votre propre application, vous avez également besoin des implémentations de plateforme incluses dans la solution **TouchTrackingEffectDemos**.

## <a name="the-touch-tracking-effect-implementations"></a>Implémentations de l’effet de suivi de tactile

Les implémentations iOS, Android et UWP de `TouchEffect` sont décrites ci-dessous, en commençant avec l’implémentation la plus simple (UWP) et en finissant par l’implémentation iOS, car elle est d’une plus grande complexité structurelle que les autres.

### <a name="the-uwp-implementation"></a>Implémentation UWP

L’implémentation UWP de `TouchEffect` est la plus simple. Comme d’habitude, la classe dérive de `PlatformEffect` et inclut deux attributs d’assembly :

```csharp
[assembly: ResolutionGroupName("XamarinDocs")]
[assembly: ExportEffect(typeof(TouchTracking.UWP.TouchEffect), "TouchEffect")]

namespace TouchTracking.UWP
{
    public class TouchEffect : PlatformEffect
    {
        ...
    }
}
```

Le remplacement de `OnAttached` enregistre certaines informations sous forme de champs et attache des gestionnaires à tous les événements de pointeur :

```csharp
public class TouchEffect : PlatformEffect
{
    FrameworkElement frameworkElement;
    TouchTracking.TouchEffect effect;
    Action<Element, TouchActionEventArgs> onTouchAction;

    protected override void OnAttached()
    {
        // Get the Windows FrameworkElement corresponding to the Element that the effect is attached to
        frameworkElement = Control == null ? Container : Control;

        // Get access to the TouchEffect class in the .NET Standard library
        effect = (TouchTracking.TouchEffect)Element.Effects.
                    FirstOrDefault(e => e is TouchTracking.TouchEffect);

        if (effect != null && frameworkElement != null)
        {
            // Save the method to call on touch events
            onTouchAction = effect.OnTouchAction;

            // Set event handlers on FrameworkElement
            frameworkElement.PointerEntered += OnPointerEntered;
            frameworkElement.PointerPressed += OnPointerPressed;
            frameworkElement.PointerMoved += OnPointerMoved;
            frameworkElement.PointerReleased += OnPointerReleased;
            frameworkElement.PointerExited += OnPointerExited;
            frameworkElement.PointerCanceled += OnPointerCancelled;
        }
    }
    ...
}    
```

Le gestionnaire `OnPointerPressed` appelle l’événement d’effet en appelant le champ `onTouchAction` de la méthode `CommonHandler` :

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerPressed(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Pressed, args);

        // Check setting of Capture property
        if (effect.Capture)
        {
            (sender as FrameworkElement).CapturePointer(args.Pointer);
        }
    }
    ...
    void CommonHandler(object sender, TouchActionType touchActionType, PointerRoutedEventArgs args)
    {
        PointerPoint pointerPoint = args.GetCurrentPoint(sender as UIElement);
        Windows.Foundation.Point windowsPoint = pointerPoint.Position;  

        onTouchAction(Element, new TouchActionEventArgs(args.Pointer.PointerId,
                                                        touchActionType,
                                                        new Point(windowsPoint.X, windowsPoint.Y),
                                                        args.Pointer.IsInContact));
    }
}
```

`OnPointerPressed` vérifie également la valeur de la propriété `Capture` dans la classe d’effet de la bibliothèque .NET Standard, et appelle `CapturePointer` si la valeur est `true`.

 Les autres gestionnaires d’événements UWP sont encore plus simples :

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerEntered(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Entered, args);
    }
    ...
}
```

### <a name="the-android-implementation"></a>Implémentation Android

Les implémentations iOS et Android sont nécessairement plus complexes, car elles doivent implémenter les événements `Exited` et `Entered` quand un doigt se déplace d’un élément à un autre. Les deux implémentations sont structurées de façon similaire.

La classe `TouchEffect` d’Android installe un gestionnaire pour l’événement `Touch` :

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

La classe définit également deux dictionnaires statiques :

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    static Dictionary<Android.Views.View, TouchEffect> viewDictionary =
        new Dictionary<Android.Views.View, TouchEffect>();

    static Dictionary<int, TouchEffect> idToEffectDictionary =
        new Dictionary<int, TouchEffect>();
    ...
```

`viewDictionary` reçoit une nouvelle entrée chaque fois que le remplacement de `OnAttached` est appelé :

```csharp
viewDictionary.Add(view, this);
```

L’entrée est supprimée du dictionnaire dans `OnDetached`. Chaque instance de `TouchEffect` est associée à une vue particulière à laquelle l’effet est attaché. Le dictionnaire statique permet à n’importe quelle instance de `TouchEffect` d’énumérer toutes les autres vues et leurs instances de `TouchEffect` correspondantes. Ceci est nécessaire pour permettre le transfert des événements d’une vue à l’autre.

Android affecte un code d’ID aux événements tactiles qui permet à une application de suivre des doigts individuels. `idToEffectDictionary` associe ce code d’ID à une instance de `TouchEffect`. Un élément est ajouté à ce dictionnaire quand le gestionnaire `Touch` est appelé pour un appui avec un doigt :

```csharp
void OnTouch(object sender, Android.Views.View.TouchEventArgs args)
{
    ...
    switch (args.Event.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:
            FireEvent(this, id, TouchActionType.Pressed, screenPointerCoords, true);

            idToEffectDictionary.Add(id, this);

            capture = libTouchEffect.Capture;
            break;

```

L’élément est supprimé de `idToEffectDictionary` quand le doigt est détaché de l’écran. La méthode `FireEvent` accumule simplement toutes les informations nécessaires pour appeler la méthode `OnTouchAction` :

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.libTouchEffect.OnTouchAction;

    // Get the location of the pointer within the view
    touchEffect.view.GetLocationOnScreen(twoIntArray);
    double x = pointerLocation.X - twoIntArray[0];
    double y = pointerLocation.Y - twoIntArray[1];
    Point point = new Point(fromPixels(x), fromPixels(y));

    // Call the method
    onTouchAction(touchEffect.formsElement,
        new TouchActionEventArgs(id, actionType, point, isInContact));
}
```

Tous les autres types d’événements tactiles sont traités de deux façons différentes : si la propriété `Capture` est `true`, l’événement tactile est une traduction relativement simple en informations pour `TouchEffect`. Cela devient plus compliqué quand `Capture` est `false`, car les événements tactiles doivent alors être déplacés d’une vue à une autre. C’est de la responsabilité de la méthode `CheckForBoundaryHop`, qui est appelée lors des événements de déplacement. Cette méthode utilise les deux dictionnaires statiques. Elle énumère `viewDictionary` pour déterminer la vue que le doigt touche actuellement, et elle utilise `idToEffectDictionary` pour stocker l’instance active de `TouchEffect` (et par conséquent la vue active) associée à un ID spécifique :

```csharp
void CheckForBoundaryHop(int id, Point pointerLocation)
{
    TouchEffect touchEffectHit = null;

    foreach (Android.Views.View view in viewDictionary.Keys)
    {
        // Get the view rectangle
        try
        {
            view.GetLocationOnScreen(twoIntArray);
        }
        catch // System.ObjectDisposedException: Cannot access a disposed object.
        {
            continue;
        }
        Rectangle viewRect = new Rectangle(twoIntArray[0], twoIntArray[1], view.Width, view.Height);

        if (viewRect.Contains(pointerLocation))
        {
            touchEffectHit = viewDictionary[view];
        }
    }

    if (touchEffectHit != idToEffectDictionary[id])
    {
        if (idToEffectDictionary[id] != null)
        {
            FireEvent(idToEffectDictionary[id], id, TouchActionType.Exited, pointerLocation, true);
        }
        if (touchEffectHit != null)
        {
            FireEvent(touchEffectHit, id, TouchActionType.Entered, pointerLocation, true);
        }
        idToEffectDictionary[id] = touchEffectHit;
    }
}
```

S’il y a eu un changement dans `idToEffectDictionary`, la méthode appelle éventuellement `FireEvent` pour `Exited` et `Entered` afin de passer d’une vue à l’autre. Cependant, le doigt peut avoir été déplacé dans une zone occupée par une vue sans instance de `TouchEffect` attachée, ou de cette zone dans une vue avec l’effet attaché.

Notez le bloc `try` et `catch` lors de l’accès à la vue. Lors d’un accès à une page suivi d’un retour à la page d’accueil, la méthode `OnDetached` n’est pas appelée, et les éléments restent dans `viewDictionary`, mais Android les considère comme supprimés.

### <a name="the-ios-implementation"></a>Implémentation iOS

L’implémentation iOS est similaire à l’implémentation Android, sauf que la classe `TouchEffect` d’iOS doit instancier un dérivé de `UIGestureRecognizer`. Il s’agit d’une classe dans le projet iOS nommée `TouchRecognizer`. Cette classe gère deux dictionnaires statiques qui stockent les instances de `TouchRecognizer` :

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

La plus grande partie de la structure de cette classe `TouchRecognizer` est similaire à la classe `TouchEffect` d’Android.

> [!IMPORTANT]
> Un grand nombre des vues dans `UIKit` n’ont pas la fonctionnalité tactile activée par défaut. La fonctionnalité tactile peut être activée en ajoutant `view.UserInteractionEnabled = true;` au remplacement `OnAttached` dans la classe `TouchEffect` du projet iOS. Cela doit se produire après l’obtention de `UIView` qui correspond à l’élément auquel l’effet est joint.

## <a name="putting-the-touch-effect-to-work"></a>Utilisation de l’effet tactile

Le programme [**TouchTrackingEffectDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) contient cinq pages qui testent l’effet de suivi tactile pour des tâches courantes.

La page **BoxView Dragging** vous permet d’ajouter des éléments `BoxView` à `AbsoluteLayout`, puis de les faire glisser sur l’écran. Le [fichier XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml) instancie deux vues `Button` pour l’ajout d’éléments `BoxView` à `AbsoluteLayout` et pour l’effacement de `AbsoluteLayout`.

La méthode dans le [fichier code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml.cs) qui ajoute un nouvel élément `BoxView` à `AbsoluteLayout` ajoute également un objet `TouchEffect` à `BoxView` et attache un gestionnaire d’événements à l’effet :

```csharp
void AddBoxViewToLayout()
{
    BoxView boxView = new BoxView
    {
        WidthRequest = 100,
        HeightRequest = 100,
        Color = new Color(random.NextDouble(),
                          random.NextDouble(),
                          random.NextDouble())
    };

    TouchEffect touchEffect = new TouchEffect();
    touchEffect.TouchAction += OnTouchEffectAction;
    boxView.Effects.Add(touchEffect);
    absoluteLayout.Children.Add(boxView);
}
```

Le gestionnaire d’événements `TouchAction` traite tous les événements tactiles pour tous les éléments `BoxView`, mais il doit procéder avec une certaine prudence : il ne peut pas autoriser deux doigts sur un même élément `BoxView`, car le programme implémente seulement l’opération de glisser et il y aurait une interférence entre les deux doigts. Pour cette raison, la page définit une classe incorporée pour chaque doigt suivi :

```csharp
class DragInfo
{
    public DragInfo(long id, Point pressPoint)
    {
        Id = id;
        PressPoint = pressPoint;
    }

    public long Id { private set; get; }

    public Point PressPoint { private set; get; }
}

Dictionary<BoxView, DragInfo> dragDictionary = new Dictionary<BoxView, DragInfo>();
```

`dragDictionary` contient une entrée pour chaque `BoxView` faisant l’objet d’une opération de glisser.

L’action tactile `Pressed` ajoute un élément à ce dictionnaire, et l’action `Released` le supprime. La logique de `Pressed` doit vérifier s’il existe déjà un élément dans le dictionnaire pour cet élément `BoxView`. Le cas échéant, cela signifie que l’élément `BoxView` fait déjà l’objet d’une opération de glisser et que le nouvel événement est un deuxième doigt sur ce même élément `BoxView`. Pour les actions `Moved` et `Released`, le gestionnaire d’événements doit vérifier si le dictionnaire a une entrée pour cet élément `BoxView` et que la propriété `Id` de l’événement tactile pour cet élément `BoxView` glissé correspond à celui de l’entrée du dictionnaire :

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    BoxView boxView = sender as BoxView;

    switch (args.Type)
    {
        case TouchActionType.Pressed:
            // Don't allow a second touch on an already touched BoxView
            if (!dragDictionary.ContainsKey(boxView))
            {
                dragDictionary.Add(boxView, new DragInfo(args.Id, args.Location));

                // Set Capture property to true
                TouchEffect touchEffect = (TouchEffect)boxView.Effects.FirstOrDefault(e => e is TouchEffect);
                touchEffect.Capture = true;
            }
            break;

        case TouchActionType.Moved:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                Rectangle rect = AbsoluteLayout.GetLayoutBounds(boxView);
                Point initialLocation = dragDictionary[boxView].PressPoint;
                rect.X += args.Location.X - initialLocation.X;
                rect.Y += args.Location.Y - initialLocation.Y;
                AbsoluteLayout.SetLayoutBounds(boxView, rect);
            }
            break;

        case TouchActionType.Released:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                dragDictionary.Remove(boxView);
            }
            break;
    }
}
```

La logique de `Pressed` définit la propriété `Capture` de l’objet `TouchEffect` sur `true`. Ceci a pour effet de délivrer tous les événements suivants pour ce doigt au même gestionnaire d’événements.

La logique de `Moved` déplace l’élément `BoxView` en modifiant la propriété attachée `LayoutBounds`. La propriété `Location` des arguments de l’événement est toujours relative à l’élément `BoxView` faisant l’objet d’une opération de glisser, et si l’élément `BoxView` est déplacé à une vitesse constante, les propriétés `Location` des événements consécutifs seront approximativement les mêmes. Par exemple, si un doigt appuie sur l’élément `BoxView` en son centre, l’action `Pressed` stocke une propriété `PressPoint` de (50, 50), qui reste la même pour les événements suivants. Si l’élément `BoxView` est déplacé en diagonale à une vitesse constante, les propriétés `Location` suivantes pendant l’action `Moved` peuvent avoir des valeurs (55, 55), auquel cas la logique de `Moved` ajoute 5 à la position horizontale et verticale de l’élément `BoxView`. Ceci opération déplace l’élément `BoxView` de façon à ce que son centre se trouve à nouveau directement sous le doigt.

Vous pouvez déplacer plusieurs éléments `BoxView` simultanément en utilisant des doigts différents.

[![](touch-tracking-images/boxviewdragging-small.png "Triple screenshot of the BoxView Dragging page")](touch-tracking-images/boxviewdragging-large.png#lightbox "Triple screenshot of the BoxView Dragging page")

### <a name="subclassing-the-view"></a>Sous-classement de la vue

Il est souvent plus facile pour un élément de Xamarin.Forms de gérer ses propres événements tactiles. La page **Draggable BoxView Dragging** fonctionne comme la page **BoxView Dragging**, mais les éléments que l’utilisateur fait glisser sont des instances d’une classe [`DraggableBoxView`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/DraggableBoxView.cs) qui dérive de `BoxView` :

```csharp
class DraggableBoxView : BoxView
{
    bool isBeingDragged;
    long touchId;
    Point pressPoint;

    public DraggableBoxView()
    {
        TouchEffect touchEffect = new TouchEffect
        {
            Capture = true
        };
        touchEffect.TouchAction += OnTouchEffectAction;
        Effects.Add(touchEffect);
    }

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!isBeingDragged)
                {
                    isBeingDragged = true;
                    touchId = args.Id;
                    pressPoint = args.Location;
                }
                break;

            case TouchActionType.Moved:
                if (isBeingDragged && touchId == args.Id)
                {
                    TranslationX += args.Location.X - pressPoint.X;
                    TranslationY += args.Location.Y - pressPoint.Y;
                }
                break;

            case TouchActionType.Released:
                if (isBeingDragged && touchId == args.Id)
                {
                    isBeingDragged = false;
                }
                break;
        }
    }
}
```

Le constructeur crée et attache l’élément `TouchEffect` et définit la propriété `Capture` quand cet objet est instancié pour la première fois. Aucun dictionnaire n’est nécessaire, car la classe stocke elle-même les valeurs `isBeingDragged`, `pressPoint` et `touchId` associées à chaque doigt. La gestion de `Moved` modifie les propriétés `TranslationX` et `TranslationY` : la logique fonctionne donc même si le parent de `DraggableBoxView` n’est pas un élément `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>Intégration à SkiaSharp

Les deux démonstrations suivantes nécessitent des graphiques, et elles utilisent pour cela SkiaSharp. Si vous le souhaitez, avant d’examiner ces exemples, vous pouvez consulter des informations sur l’[utilisation de SkiaSharp dans Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md). Les deux premiers articles (« SkiaSharp Drawing Basics » et « SkiaSharp Lines and Paths ») couvrent tout ce dont vous avez besoin ici.

La page **Ellipse Drawing** vous permet de dessiner une ellipse en faisant glisser votre doigt sur l’écran. Selon la façon dont vous déplacez votre doigt, vous pouvez dessiner l’ellipse en partant du coin supérieur gauche vers le coin inférieur droit, ou en partant de n’importe quel autre coin vers le coin opposé. L’ellipse est dessinée avec une couleur et une opacité aléatoires.

[![](touch-tracking-images/ellipsedrawing-small.png "Triple screenshot of the Ellipse Drawing page")](touch-tracking-images/ellipsedrawing-large.png#lightbox "Triple screenshot of the Ellipse Drawing page")

Si vous touchez ensuite une des ellipses, vous pouvez la faire glisser vers un autre emplacement. Ceci nécessite une technique appelée « test d’atteinte » (hit-testing), qui implique la recherche de l’objet graphique à un point donné. Les ellipses de SkiaSharp ne sont pas des éléments de Xamarin.Forms : ils n’effectuent donc pas leur propre traitement de `TouchEffect`. Le `TouchEffect` doit s’appliquer à tout l’objet `SKCanvasView`.

Le fichier [EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml) instancie la `SKCanvasView` dans une `Grid` avec une seule cellule. L’objet `TouchEffect` est attaché à cette `Grid` :

```xaml
<Grid x:Name="canvasViewGrid"
        Grid.Row="1"
        BackgroundColor="White">

    <skia:SKCanvasView x:Name="canvasView"
                        PaintSurface="OnCanvasViewPaintSurface" />
    <Grid.Effects>
        <tt:TouchEffect Capture="True"
                        TouchAction="OnTouchEffectAction" />
    </Grid.Effects>
</Grid>
```

Dans Android et la plateforme Windows universelle, l’élément `TouchEffect` peut être attaché directement à l’élément `SKCanvasView`, mais sur iOS, cela ne fonctionne pas. Notez que la propriété `Capture` est définie sur `true`.

Chaque ellipse rendue par SkiaSharp est représentée par un objet de type `EllipseDrawingFigure` :

```csharp
class EllipseDrawingFigure
{
    SKPoint pt1, pt2;

    public EllipseDrawingFigure()
    {
    }

    public SKColor Color { set; get; }

    public SKPoint StartPoint
    {
        set
        {
            pt1 = value;
            MakeRectangle();
        }
    }

    public SKPoint EndPoint
    {
        set
        {
            pt2 = value;
            MakeRectangle();
        }
    }

    void MakeRectangle()
    {
        Rectangle = new SKRect(pt1.X, pt1.Y, pt2.X, pt2.Y).Standardized;
    }

    public SKRect Rectangle { set; get; }

    // For dragging operations
    public Point LastFingerLocation { set; get; }

    // For the dragging hit-test
    public bool IsInEllipse(SKPoint pt)
    {
        SKRect rect = Rectangle;

        return (Math.Pow(pt.X - rect.MidX, 2) / Math.Pow(rect.Width / 2, 2) +
                Math.Pow(pt.Y - rect.MidY, 2) / Math.Pow(rect.Height / 2, 2)) < 1;
    }
}
```

Les propriétés `StartPoint` et `EndPoint` sont utilisées quand le programme traite une entrée tactile ; la propriété `Rectangle` est utilisée pour dessiner l’ellipse. La propriété `LastFingerLocation` intervient quand l’ellipse est déplacée, et la méthode `IsInEllipse` participe au test d’atteinte. La méthode retourne `true` si le point est à l’intérieur de l’ellipse.

Le [fichier code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml.cs) gère trois collections :

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

Le dictionnaire `draggingFigure` contient un sous-ensemble de la collection `completedFigures`. Le gestionnaire d’événements `PaintSurface` de SkiaSharp rend simplement les objets de ces collections `completedFigures` et `inProgressFigures` :

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Fill
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (EllipseDrawingFigure figure in completedFigures)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
    foreach (EllipseDrawingFigure figure in inProgressFigures.Values)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
}
```

La partie la plus délicate du traitement de l’événement tactile est la gestion de `Pressed`. C’est là où le test d’atteinte est effectué, mais si le code détecte une ellipse sous le doigt de l’utilisateur, cette ellipse peut être déplacée seulement si elle n’est pas en cours de déplacement par un autre doigt. S’il n’existe pas d’ellipse sous le doigt de l’utilisateur, le code commence le processus de dessin d’une nouvelle ellipse :

```csharp
case TouchActionType.Pressed:
    bool isDragOperation = false;

    // Loop through the completed figures
    foreach (EllipseDrawingFigure fig in completedFigures.Reverse<EllipseDrawingFigure>())
    {
        // Check if the finger is touching one of the ellipses
        if (fig.IsInEllipse(ConvertToPixel(args.Location)))
        {
            // Tentatively assume this is a dragging operation
            isDragOperation = true;

            // Loop through all the figures currently being dragged
            foreach (EllipseDrawingFigure draggedFigure in draggingFigures.Values)
            {
                // If there's a match, we'll need to dig deeper
                if (fig == draggedFigure)
                {
                    isDragOperation = false;
                    break;
                }
            }

            if (isDragOperation)
            {
                fig.LastFingerLocation = args.Location;
                draggingFigures.Add(args.Id, fig);
                break;
            }
        }
    }

    if (isDragOperation)
    {
        // Move the dragged ellipse to the end of completedFigures so it's drawn on top
        EllipseDrawingFigure fig = draggingFigures[args.Id];
        completedFigures.Remove(fig);
        completedFigures.Add(fig);
    }
    else // start making a new ellipse
    {
        // Random bytes for random color
        byte[] buffer = new byte[4];
        random.NextBytes(buffer);

        EllipseDrawingFigure figure = new EllipseDrawingFigure
        {
            Color = new SKColor(buffer[0], buffer[1], buffer[2], buffer[3]),
            StartPoint = ConvertToPixel(args.Location),
            EndPoint = ConvertToPixel(args.Location)
        };
        inProgressFigures.Add(args.Id, figure);
    }
    canvasView.InvalidateSurface();
    break;
```

L’autre exemple SkiaSharp est la page **Finger Paint**. Vous pouvez sélectionner une couleur de trait et une épaisseur de trait dans deux vues `Picker`, puis dessiner avec un ou plusieurs doigts :

[![](touch-tracking-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](touch-tracking-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

Cet exemple nécessite également une classe distincte pour représenter chaque ligne peinte à l’écran :

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new SKPath();
    }

    public SKPath Path { set; get; }

    public Color StrokeColor { set; get; }

    public float StrokeWidth { set; get; }
}
```

Un objet `SKPath` est utilisé pour rendre chaque ligne. Le fichier [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/FingerPaintPage.xaml.cs) gère deux collections de ces objets, une pour ces polylignes en cours de dessin et une autre pour les polylignes terminées :

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Le traitement de `Pressed` crée une `FingerPaintPolyline`, appelle `MoveTo` sur l’objet de chemin pour stocker le point initial et ajoute cet objet au dictionnaire `inProgressPolylines`. Le traitement de `Moved` appelle `LineTo` sur l’objet de chemin avec la nouvelle position du doigt, et le traitement de `Released` transfère la polyligne terminée de `inProgressPolylines` vers `completedPolylines`. À nouveau, le code gérant le dessin de SkiaSharp est relativement simple :

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    StrokeCap = SKStrokeCap.Round,
    StrokeJoin = SKStrokeJoin.Round
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (FingerPaintPolyline polyline in completedPolylines)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }

    foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }
}
```

### <a name="tracking-view-to-view-touch"></a>Suivi de l’interaction tactile de vue à vue

Tous les exemples précédents ont défini la propriété `Capture` de `TouchEffect` sur `true`, quand `TouchEffect` a été créé ou quand l’événement `Pressed` s’est produit. Ceci garantit que le même élément reçoit tous les événements associés au doigt qui a appuyé en premier sur la vue. L’exemple final ne définit *pas*`Capture` sur `true`. Ceci provoque un comportement différent quand un doigt en contact avec l’écran se déplace d’un élément à un autre. L’élément déplacé par le doigt reçoit un événement avec une propriété `Type` définie sur `TouchActionType.Exited` et le deuxième élément reçoit un événement avec un `Type` défini sur `TouchActionType.Entered`.

Ce type de traitement d’événement tactile est très pratique pour un clavier musical. Une touche doit être en mesure de détecter quand l’utilisateur l’enfonce, mais également quand un doigt glisse d’une touche à une autre.

La page **Silent Keyboard** définit des petites classes [`WhiteKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/WhiteKey.cs) et [`BlackKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BlackKey.cs) qui dérivent de [`Key`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/Key.cs), qui dérive elle-même de `BoxView`.

La classe `Key` est prête à être utilisée dans un programme musical réel. Elle définit des propriétés publiques nommées `IsPressed` et `KeyNumber`, cette dernière étant destinée à être définie sur le code de la touche tel qu’il est établi par le standard MIDI. La classe `Key` définit également un événement nommé `StatusChanged`, qui est appelé quand la propriété `IsPressed` change.

Plusieurs doigts sont autorisés sur chaque touche. Pour cette raison, la classe `Key` gère un élément `List` des numéros d’ID d’interaction tactile de tous les doigts touchant actuellement cette touche :

```csharp
List<long> ids = new List<long>();
```

Le gestionnaire d’événements `TouchAction` ajoute un ID à la liste `ids` pour un type d’événement `Pressed` et pour un type `Entered`, mais seulement quand la propriété `IsInContact` est `true` pour l’événement `Entered`. L’ID est supprimé de la `List` pour un événement `Released` ou `Exited` :

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    switch (args.Type)
    {
      case TouchActionType.Pressed:
          AddToList(args.Id);
          break;

        case TouchActionType.Entered:
            if (args.IsInContact)
            {
                AddToList(args.Id);
            }
            break;

        case TouchActionType.Moved:
            break;

        case TouchActionType.Released:
        case TouchActionType.Exited:
            RemoveFromList(args.Id);
            break;
    }
}

```

Les méthodes `AddToList` et `RemoveFromList` vérifient toutes deux si l’élément `List` a changé de vide en non vide ou inversement et, si tel est le cas, elles appellent l’événement `StatusChanged`.

Les différents éléments `WhiteKey` et `BlackKey` sont organisés dans le [fichier XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/SilentKeyboardPage.xaml) de la page, ce qui convient mieux quand le téléphone est tenu en mode paysage :

[![](touch-tracking-images/silentkeyboard-small.png "Triple screenshot of the Silent Keyboard page")](touch-tracking-images/silentkeyboard-large.png#lightbox "Triple screenshot of the Silent Keyboard page")

Si vous balayez avec votre doigt sur les touches, vous voyez grâce à de légères modifications de couleur que les événements tactiles sont transférés d’une touche à une autre.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment appeler des événements dans un effet, et comment écrire et utiliser un effet qui implémente un traitement de l’interaction tactile multipoint de bas niveau.

## <a name="related-links"></a>Liens connexes

- [Suivi de l’interaction tactile multipoint dans iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Suivi de l’interaction tactile multipoint dans Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [Effet de suivi tactile (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
