---
title: Ajout d’un module de reconnaissance des appuis
description: Cet article explique comment utiliser le geste d’appui pour la détection des appuis dans une application Xamarin.Forms. La détection des appuis est implémentée avec la classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 95f25dbce55e2b960f604b6e304ffb6e8ed775e0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771335"
---
# <a name="adding-a-tap-gesture-recognizer"></a>Ajout d’un module de reconnaissance des appuis

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_Le geste d’appui est utilisé pour la détection des appuis, il est implémentée avec la classe TapGestureRecognizer._

Pour qu’un élément d’interface utilisateur réagisse à un appui, créez une instance [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer), traitez l’événement [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) et ajoutez le nouveau module de reconnaissance de geste à la collection [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) sur l’élément d’interface utilisateur. L'exemple de code suivant montre un `TapGestureRecognizer` attaché à un élément [`Image`](xref:Xamarin.Forms.Image) :

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Par défaut, l’image répond aux appuis uniques. Définissez la propriété [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) pour attendre un double-appui (ou plusieurs appuis si nécessaire).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quand [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) a une valeur supérieure à 1, le gestionnaire d’événements est exécuté uniquement si les appuis se produisent dans un intervalle de temps donné (cette période n’est pas configurable). Si les appuis suivants (le deuxième ou les autres) ne se produisent pas dans cette période, ils sont effectivement ignorés et le « nombre d’appuis » redémarre.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Utilisation de Xaml

Vous pouvez ajouter un module de reconnaissance de geste à un contrôle en Xaml à l’aide des propriétés jointes. La syntaxe pour ajouter un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) à une image est indiquée ci-dessous (dans ce cas, en définissant un événement *double appui*) :

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

Le code du gestionnaire d’événements (dans l’exemple) incrémente un compteur et remplace l’image en couleur par une image en noir et blanc.

```csharp
void OnTapGestureRecognizerTapped(object sender, EventArgs args)
{
    tapCount++;
    var imageSender = (Image)sender;
    // watch the monkey go from color to black&white!
    if (tapCount % 2 == 0) {
        imageSender.Source = "tapped.jpg";
    } else {
        imageSender.Source = "tapped_bw.jpg";
    }
}
```

## <a name="using-icommand"></a>Utilisation de ICommand

Les applications qui utilisent modèle-vue-vue modèle (MVVM) se servent généralement d’`ICommand` plutôt que des gestionnaires d’événements. [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) peut facilement prendre en charge `ICommand` en définissant la liaison dans le code :

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

ou en utilisant Xaml :

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

Le code complet pour ce modèle de vue se trouve dans l’exemple. Les informations pertinentes sur l’implémentation de `Command` sont présentées ci-dessous :

```csharp
public class TapViewModel : INotifyPropertyChanged
{
    int taps = 0;
    ICommand tapCommand;
    public TapViewModel () {
        // configure the TapCommand with a method
        tapCommand = new Command (OnTapped);
    }
    public ICommand TapCommand {
        get { return tapCommand; }
    }
    void OnTapped (object s)  {
        taps++;
        Debug.WriteLine ("parameter: " + s);
    }
    //region INotifyPropertyChanged code omitted
}
```

## <a name="related-links"></a>Liens associés

- [TapGesture (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
