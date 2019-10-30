---
title: Suivi des doigts multipoint dans Xamarin. Android
description: Cette rubrique montre comment suivre les événements tactiles à partir de plusieurs doigts
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: f960c3cec90bd331f5a1433a869c7720b40c9680
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024268"
---
# <a name="multi-touch-finger-tracking"></a>Suivi des doigts multipoint

_Cette rubrique montre comment suivre les événements tactiles à partir de plusieurs doigts_

Il arrive parfois qu’une application multipoint doive suivre les doigts individuels lorsqu’ils se déplacent simultanément sur l’écran. Une application classique est un programme de peinture par doigt. Vous souhaitez que l’utilisateur soit en mesure de dessiner avec un seul doigt, mais également de dessiner avec plusieurs doigts à la fois. À mesure que votre programme traite plusieurs événements tactiles, il doit distinguer les événements qui correspondent à chaque doigt. Android fournit un code d’ID à cet effet, mais l’obtention et la gestion de ce code peuvent être un peu délicates.

Pour tous les événements associés à un doigt particulier, le code d’ID reste le même. Le code d’ID est affecté lorsqu’un doigt touche d’abord l’écran et devient non valide après que le doigt s’est levé à partir de l’écran.
Ces codes d’ID sont généralement très petits entiers et Android les réutilise pour les événements tactiles ultérieurs.

Presque toujours, un programme qui suit des doigts individuels conserve un dictionnaire pour le suivi tactile. La clé du dictionnaire est le code d’ID qui identifie un doigt particulier. La valeur du dictionnaire dépend de l’application. Dans le programme [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) , chaque trait de doigt (du toucher au lancement) est associé à un objet qui contient toutes les informations nécessaires pour afficher la ligne dessinée avec ce doigt. Le programme définit une petite classe de `FingerPaintPolyline` à cet effet :

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

Chaque polyligne a une couleur, une largeur de trait et un objet [`Path`](xref:Android.Graphics.Path) Android Graphics pour accumuler et afficher plusieurs points de la ligne lorsqu’elle est dessinée.

Le reste du code présenté ci-dessous est contenu dans une `View` dérivée nommée `FingerPaintCanvasView`. Cette classe gère un dictionnaire d’objets de type `FingerPaintPolyline` pendant la durée pendant laquelle ils sont activement dessinés par un ou plusieurs doigts :

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Ce dictionnaire permet à la vue d’obtenir rapidement les informations `FingerPaintPolyline` associées à un doigt particulier.

La classe `FingerPaintCanvasView` gère également un objet `List` pour les polylignes qui ont été terminées :

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Les objets de cette `List` se trouvent dans l’ordre dans lequel ils ont été dessinés.

`FingerPaintCanvasView` remplace deux méthodes définies par `View`: [`OnDraw`](xref:Android.Views.View.OnDraw*)
et [`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*).
Dans sa `OnDraw` remplacement, la vue dessine les polylignes terminées, puis dessine les polylignes en cours.

La substitution de la méthode `OnTouchEvent` commence par l’obtention d’une valeur `pointerIndex` à partir de la propriété `ActionIndex`. Cette valeur de `ActionIndex` fait la distinction entre plusieurs doigts, mais elle n’est pas cohérente entre plusieurs événements. Pour cette raison, vous utilisez la `pointerIndex` pour obtenir la valeur du pointeur `id` à partir de la méthode `GetPointerId`. Cet ID *est* cohérent sur plusieurs événements :

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

Notez que la substitution utilise la propriété `ActionMasked` dans l’instruction `switch` plutôt que la propriété `Action`. En voici les raisons :

Lorsque vous travaillez avec l’interface multipoint, la propriété `Action` a la valeur `MotionEventsAction.Down` pour que le premier doigt touche l’écran, puis les valeurs de `Pointer2Down` et `Pointer3Down` comme deuxième et troisième doigts touchent également l’écran. Étant donné que les quatrième et cinquième doigts rendent contact, la propriété `Action` a des valeurs numériques qui ne correspondent même pas aux membres de l’énumération `MotionEventsAction` ! Vous devez examiner les valeurs des indicateurs binaires dans les valeurs pour interpréter ce qu’ils signifient.

De même, étant donné que les doigts laissent contact avec l’écran, la propriété `Action` a les valeurs `Pointer2Up` et `Pointer3Up` pour les deuxième et troisième doigts, et `Up` pour le premier doigt.

La propriété `ActionMasked` prend moins de valeurs, car elle est destinée à être utilisée conjointement avec la propriété `ActionIndex` pour faire la distinction entre plusieurs doigts. Lorsque les doigts touchent l’écran, la propriété peut uniquement être égale `MotionEventActions.Down` pour le premier doigt et `PointerDown` pour les doigts suivants. À mesure que les doigts laissent l’écran, `ActionMasked` a des valeurs de `Pointer1Up` pour les doigts suivants et `Up` pour le premier doigt.

Lors de l’utilisation de `ActionMasked`, le `ActionIndex` fait la distinction entre les doigts suivants pour toucher et sortir de l’écran, mais vous n’avez généralement pas besoin d’utiliser cette valeur, sauf en tant qu’argument d’autres méthodes de l’objet `MotionEvent`. Pour l’interaction multipoint, l’une des plus importantes de ces méthodes est `GetPointerId` appelée dans le code ci-dessus. Cette méthode retourne une valeur que vous pouvez utiliser pour une clé de dictionnaire pour associer des événements particuliers à des doigts.

Le `OnTouchEvent` remplacement dans le programme [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) traite les événements `MotionEventActions.Down` et `PointerDown` de la même manière en créant un nouvel objet `FingerPaintPolyline` et en l’ajoutant au dictionnaire :

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

Notez que le `pointerIndex` est également utilisé pour obtenir la position du doigt dans la vue. Toutes les informations tactiles sont associées à la valeur `pointerIndex`. Le `id` identifie de façon unique les doigts sur plusieurs messages, ce qui permet de créer l’entrée de dictionnaire.

De même, le `OnTouchEvent` remplacement gère également le `MotionEventActions.Up` et `Pointer1Up` de la même façon en transférant la polyligne terminée à la collection de `completedPolylines` afin qu’elles puissent être dessinées pendant la substitution de `OnDraw`. Le code supprime également l’entrée `id` du dictionnaire :

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

Maintenant, pour une partie délicate.

En général, il existe de nombreux événements de `MotionEventActions.Move`. Celles-ci sont regroupées dans un seul appel à `OnTouchEvent`, et elles doivent être gérées différemment des événements `Down` et `Up`. La valeur `pointerIndex` obtenue précédemment à partir de la propriété `ActionIndex` doit être ignorée. Au lieu de cela, la méthode doit obtenir plusieurs valeurs de `pointerIndex` en effectuant une boucle entre 0 et la propriété `PointerCount`, puis obtenir une `id` pour chacune de ces `pointerIndex` valeurs :

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

Ce type de traitement permet au programme [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) de suivre des doigts individuels et de dessiner les résultats à l’écran :

[![exemple de capture d’écran de l’exemple FingerPaint](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Vous avez maintenant vu comment vous pouvez suivre des doigts individuels sur l’écran et les distinguer.

## <a name="related-links"></a>Liens associés

- [Guide Xamarin iOS équivalent](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
