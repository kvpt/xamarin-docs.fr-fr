---
title: 'Procédure pas à pas: utilisation de Touch dans Android'
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/09/2018
ms.openlocfilehash: d9c8fb7e1045d35fa23c85c689cb0e1f9461d8dd
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70225814"
---
# <a name="walkthrough---using-touch-in-android"></a>Procédure pas à pas: utilisation de Touch dans Android

Voyons comment utiliser les concepts de la section précédente dans une application opérationnelle. Nous allons créer une application avec quatre activités. La première activité est un menu ou un menu général qui lance les autres activités pour illustrer les différentes API. La capture d’écran suivante montre l’activité principale:

[![Exemple de capture d’écran avec le bouton tactile](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

La première activité, Touch Sample, montre comment utiliser des gestionnaires d’événements pour toucher les vues. L’activité de reconnaissance de mouvement montre comment sous-classer `Android.View.Views` et gérer les événements, ainsi que pour montrer comment gérer les gestes de pincement. La troisième et dernière activité, **mouvement personnalisé**, montre comment utiliser des mouvements personnalisés. Pour faciliter la suivi et l’absorption, nous allons rompre cette procédure pas à pas dans les sections, chaque section étant axée sur l’une des activités.

## <a name="touch-sample-activity"></a>Activité de l’exemple tactile

- Ouvrez le projet **TouchWalkthrough\_démarrer**. Le **MainActivity** est défini pour aller &ndash; jusqu’à nous pour implémenter le comportement tactile dans l’activité. Si vous exécutez l’application et cliquez sur **exemple tactile**, l’activité suivante doit démarrer:

  [![Capture d’écran de l’activité avec l’interaction tactile affichée](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

- Maintenant que nous avons confirmé que l’activité démarre, ouvrez le fichier **TouchActivity.cs** et ajoutez un gestionnaire pour l' `Touch` événement du: `ImageView`

  ```csharp
  _touchMeImageView.Touch += TouchMeImageViewOnTouch;
  ```

- Ensuite, ajoutez la méthode suivante à **TouchActivity.cs**:

  ```csharp
  private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
  {
      string message;
      switch (touchEventArgs.Event.Action & MotionEventActions.Mask)
      {
          case MotionEventActions.Down:
          case MotionEventActions.Move:
          message = "Touch Begins";
          break;

          case MotionEventActions.Up:
          message = "Touch Ends";
          break;

          default:
          message = string.Empty;
          break;
      }

      _touchInfoTextView.Text = message;
  }
  ```

Notez que dans le code ci-dessus, `Move` nous `Down` traitons l’action et comme étant identiques. En effet, même si l’utilisateur ne peut pas soulever son doigt du `ImageView`, il peut se déplacer ou la pression exercée par l’utilisateur peut changer. Ces types de modifications généreront une `Move` action.

Chaque fois que l’utilisateur touche `ImageView`le, `Touch` l’événement est déclenché et notre gestionnaire affiche le message **tactile commence** à l’écran, comme illustré dans la capture d’écran suivante:

[![Capture d’écran de l’activité avec Touch Begin](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

Tant que l’utilisateur touche le `ImageView`, **Touch commence** s’affichera dans le. `TextView` Lorsque l’utilisateur ne touche plus le `ImageView`, le message **tactile se termine** s’affiche dans la `TextView`, comme illustré dans la capture d’écran suivante:

[![Capture d’écran de l’activité avec des terminaisons tactiles](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>Activité de reconnaissance de mouvement

Permet désormais d’implémenter l’activité de reconnaissance de mouvement. Cette activité montre comment faire glisser un affichage à l’écran et illustre une façon d’implémenter le pincement à zoomer.

- Ajoutez une nouvelle activité à l’application appelée `GestureRecognizer`.
  Modifiez le code de cette activité afin qu’il ressemble au code suivant:

  ```csharp
  public class GestureRecognizerActivity : Activity
  {
      protected override void OnCreate(Bundle bundle)
      {
          base.OnCreate(bundle);
          View v = new GestureRecognizerView(this);
          SetContentView(v);
      }
  }
  ```

- Ajoutez une nouvelle vue Android au projet et nommez- `GestureRecognizerView`la. Ajoutez les variables suivantes à cette classe:

  ```csharp
  private static readonly int InvalidPointerId = -1;

  private readonly Drawable _icon;
  private readonly ScaleGestureDetector _scaleDetector;

  private int _activePointerId = InvalidPointerId;
  private float _lastTouchX;
  private float _lastTouchY;
  private float _posX;
  private float _posY;
  private float _scaleFactor = 1.0f;
  ```

- Ajoutez le constructeur suivant à `GestureRecognizerView`. Ce constructeur ajoutera `ImageView` à notre activité. À ce stade, le code ne se compile &ndash; toujours pas, nous devons créer `MyScaleListener` la classe qui vous aidera à redimensionner le `ImageView` lorsque l’utilisateur le pincera:

  ```csharp
  public GestureRecognizerView(Context context): base(context, null, 0)
  {
      _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
      _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
      _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
  }
  ```

- Pour dessiner l’image sur notre activité, nous devons remplacer la `OnDraw` méthode de la classe de vue comme indiqué dans l’extrait de code suivant. Ce code déplace le `ImageView` vers la position spécifiée par `_posX` et `_posY` , ainsi que le redimensionnement de l’image en fonction du facteur d’échelle:

  ```csharp
  protected override void OnDraw(Canvas canvas)
  {
      base.OnDraw(canvas);
      canvas.Save();
      canvas.Translate(_posX, _posY);
      canvas.Scale(_scaleFactor, _scaleFactor);
      _icon.Draw(canvas);
      canvas.Restore();
  }
  ```

- Ensuite, nous devons mettre à jour la `_scaleFactor` variable d’instance au fur et `ImageView`à mesure que l’utilisateur pince le. Nous allons ajouter une classe appelée `MyScaleListener`. Cette classe écoute les événements de mise à l’échelle qui seront déclenchés par Android lorsque l’utilisateur pincera le `ImageView`.
  Ajoutez la classe interne suivante à `GestureRecognizerView`. Cette classe est un `ScaleGesture.SimpleOnScaleGestureListener`. Cette classe est une classe pratique dans laquelle les écouteurs peuvent être sous-classes lorsque vous êtes intéressé par un sous-ensemble de mouvements:

  ```csharp
  private class MyScaleListener : ScaleGestureDetector.SimpleOnScaleGestureListener
  {
      private readonly GestureRecognizerView _view;

      public MyScaleListener(GestureRecognizerView view)
      {
          _view = view;
      }

      public override bool OnScale(ScaleGestureDetector detector)
      {
          _view._scaleFactor *= detector.ScaleFactor;

          // put a limit on how small or big the image can get.
          if (_view._scaleFactor > 5.0f)
          {
              _view._scaleFactor = 5.0f;
          }
          if (_view._scaleFactor < 0.1f)
          {
              _view._scaleFactor = 0.1f;
          }

          _view.Invalidate();
          return true;
      }
  }
  ```

- La méthode suivante à substituer dans `GestureRecognizerView` est. `OnTouchEvent` Le code suivant répertorie l’implémentation complète de cette méthode. Il y a beaucoup de code ici, donc prenez une minute et observez ce qui se passe ici. La première chose que fait cette méthode est de mettre à l' &ndash; échelle l’icône si nécessaire `_scaleDetector.OnTouchEvent`. cela est géré en appelant. Ensuite, nous essayons de déterminer quelle action a appelé cette méthode:

  - Si l’utilisateur touche l’écran avec, nous enregistrons les positions X et Y et l’ID du premier pointeur ayant touché l’écran.

  - Si l’utilisateur a déplacé son toucher à l’écran, nous constatons la distance de déplacement du pointeur par l’utilisateur.

  - Si l’utilisateur a levé son doigt sur l’écran, nous arrêterons le suivi des mouvements.

  ```csharp
  public override bool OnTouchEvent(MotionEvent ev)
  {
      _scaleDetector.OnTouchEvent(ev);

      MotionEventActions action = ev.Action & MotionEventActions.Mask;
      int pointerIndex;

      switch (action)
      {
          case MotionEventActions.Down:
          _lastTouchX = ev.GetX();
          _lastTouchY = ev.GetY();
          _activePointerId = ev.GetPointerId(0);
          break;

          case MotionEventActions.Move:
          pointerIndex = ev.FindPointerIndex(_activePointerId);
          float x = ev.GetX(pointerIndex);
          float y = ev.GetY(pointerIndex);
          if (!_scaleDetector.IsInProgress)
          {
              // Only move the ScaleGestureDetector isn't already processing a gesture.
              float deltaX = x - _lastTouchX;
              float deltaY = y - _lastTouchY;
              _posX += deltaX;
              _posY += deltaY;
              Invalidate();
          }

          _lastTouchX = x;
          _lastTouchY = y;
          break;

          case MotionEventActions.Up:
          case MotionEventActions.Cancel:
          // We no longer need to keep track of the active pointer.
          _activePointerId = InvalidPointerId;
          break;

          case MotionEventActions.PointerUp:
          // check to make sure that the pointer that went up is for the gesture we're tracking.
          pointerIndex = (int) (ev.Action & MotionEventActions.PointerIndexMask) >> (int) MotionEventActions.PointerIndexShift;
          int pointerId = ev.GetPointerId(pointerIndex);
          if (pointerId == _activePointerId)
          {
              // This was our active pointer going up. Choose a new
              // action pointer and adjust accordingly
              int newPointerIndex = pointerIndex == 0 ? 1 : 0;
              _lastTouchX = ev.GetX(newPointerIndex);
              _lastTouchY = ev.GetY(newPointerIndex);
              _activePointerId = ev.GetPointerId(newPointerIndex);
          }
          break;

      }
      return true;
  }
  ```

- Exécutez maintenant l’application et démarrez l’activité de reconnaissance de mouvement.
  Lorsqu’il démarre, l’écran doit ressembler à la capture d’écran ci-dessous:

  [![Écran de démarrage de la reconnaissance de mouvement avec l’icône Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

- À présent, touchez l’icône et faites-la glisser sur l’écran. Essayez le geste de pincement pour le zoom. À un moment donné, votre écran peut ressembler à la capture d’écran suivante:

  [![Icône déplacer des mouvements à l’écran](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

À ce stade, vous devez vous faire un Pat à l’arrière: vous venez d’implémenter pince-to-zoom dans une application Android! Passez rapidement à la troisième et dernière activité de cette procédure pas à pas &ndash; à l’aide de mouvements personnalisés.

## <a name="custom-gesture-activity"></a>Activité de mouvement personnalisée

Le dernier écran de cette procédure pas à pas utilise des mouvements personnalisés.

Dans le cadre de cette procédure pas à pas, la bibliothèque de mouvements a déjà été créée à l’aide de l’outil geste et ajoutée au projet dans le fichier Resources **/RAW/gestes**. Avec ce peu de maintenance, vous pouvez vous familiariser avec la dernière activité de la procédure pas à pas.

- Ajoutez un fichier de disposition **nommé\_Custom\_geste Layout. AXML** au projet avec le contenu suivant. Le projet contient déjà toutes les images dans le dossier **ressources** :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
      android:orientation="vertical"
      android:layout_width="match_parent"
      android:layout_height="match_parent">
      <LinearLayout
          android:layout_width="match_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <ImageView
          android:src="@drawable/check_me"
          android:layout_width="match_parent"
          android:layout_height="0dp"
          android:layout_weight="3"
          android:id="@+id/imageView1"
          android:layout_gravity="center_vertical" />
      <LinearLayout
          android:layout_width="match_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
  </LinearLayout>
  ```

- Ajoutez ensuite une nouvelle activité au projet et nommez- `CustomGestureRecognizerActivity.cs`la. Ajoutez deux variables d’instance à la classe, comme indiqué dans les deux lignes de code suivantes:

  ```csharp
  private GestureLibrary _gestureLibrary;
  private ImageView _imageView;
  ```

- Modifiez la `OnCreate` méthode de cette activité afin qu’elle ressemble au code suivant. Prenez une minute pour expliquer ce qui se passe dans ce code. La première chose que nous faisons est d' `GestureOverlayView` instancier un et de le définir comme vue racine de l’activité.
  Nous assignons également un gestionnaire d' `GesturePerformed` événements à `GestureOverlayView`l’événement de. Nous allons ensuite augmenter le fichier de disposition créé précédemment et l’ajouter en tant que vue enfant de `GestureOverlayView`. La dernière étape consiste à initialiser la variable `_gestureLibrary` et à charger le fichier de mouvements à partir des ressources de l’application. Si le fichier de mouvements ne peut pas être chargé pour une raison quelconque, il n’y a pas grand-chose que cette activité peut faire, donc elle est arrêtée:

  ```csharp
  protected override void OnCreate(Bundle bundle)
  {
      base.OnCreate(bundle);

      GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
      SetContentView(gestureOverlayView);
      gestureOverlayView.GesturePerformed += GestureOverlayViewOnGesturePerformed;

      View view = LayoutInflater.Inflate(Resource.Layout.custom_gesture_layout, null);
      _imageView = view.FindViewById<ImageView>(Resource.Id.imageView1);
      gestureOverlayView.AddView(view);

      _gestureLibrary = GestureLibraries.FromRawResource(this, Resource.Raw.gestures);
      if (!_gestureLibrary.Load())
      {
          Log.Wtf(GetType().FullName, "There was a problem loading the gesture library.");
          Finish();
      }
  }
  ```

- La dernière chose à faire est d’implémenter la `GestureOverlayViewOnGesturePerformed` méthode, comme illustré dans l’extrait de code suivant. Lorsque le `GestureOverlayView` détecte un mouvement, il rappelle cette méthode. La première chose à faire est d’essayer `IList<Prediction>` d’accéder à un objet qui correspond `_gestureLibrary.Recognize()`au geste en appelant. Nous utilisons un peu de LINQ pour obtenir le `Prediction` qui a le score le plus élevé pour le mouvement.

  S’il n’existe aucun geste correspondant avec un score suffisamment élevé, le gestionnaire d’événements se termine sans rien faire. Dans le cas contraire, nous vérifions le nom de la prédiction et modifions l’image affichée en fonction du nom du mouvement:

  ```csharp
  private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
  {
      IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
      orderby p.Score descending
      where p.Score > 1.0
      select p;
      Prediction prediction = predictions.FirstOrDefault();

      if (prediction == null)
      {
          Log.Debug(GetType().FullName, "Nothing seemed to match the user's gesture, so don't do anything.");
          return;
      }

      Log.Debug(GetType().FullName, "Using the prediction named {0} with a score of {1}.", prediction.Name, prediction.Score);

      if (prediction.Name.StartsWith("checkmark"))
      {
          _imageView.SetImageResource(Resource.Drawable.checked_me);
      }
      else if (prediction.Name.StartsWith("erase", StringComparison.OrdinalIgnoreCase))
      {
          // Match one of our "erase" gestures
          _imageView.SetImageResource(Resource.Drawable.check_me);
      }
  }
  ```

- Exécutez l’application et démarrez l’activité de reconnaissance de mouvement personnalisée. Elle doit ressembler à la capture d’écran suivante:

  [![Capture d’écran avec image de vérification](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

  À présent, dessinez une coche sur l’écran, et l’image bitmap affichée doit ressembler à ce qui est illustré dans les captures d’écran suivantes:

  [![Coche dessinée, la coche est reconnue](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

  Enfin, dessinez un griffonnage à l’écran. La case à cocher doit revenir à son image d’origine, comme illustré dans les captures d’écran suivantes:

  [![Scribble à l’écran, l’image d’origine s’affiche](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

Vous comprenez maintenant comment intégrer les fonctions tactiles et les gestes dans une application Android à l’aide de Xamarin. Android.


## <a name="related-links"></a>Liens associés

- [Android touch Start (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
