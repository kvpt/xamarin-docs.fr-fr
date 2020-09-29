---
title: Alertes dans Xamarin. Mac
description: Cet article traite de l’utilisation des alertes dans une application Xamarin. Mac. Il décrit la création et l’affichage des alertes à partir du code C# et la réponse aux interactions de l’utilisateur.
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 55c4d4e6154ef75f15b2e75edc020b7cb4c96e38
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435303"
---
# <a name="alerts-in-xamarinmac"></a>Alertes dans Xamarin. Mac

_Cet article traite de l’utilisation des alertes dans une application Xamarin. Mac. Il décrit la création et l’affichage des alertes à partir du code C# et la réponse aux interactions de l’utilisateur._

Lorsque vous travaillez avec C# et .NET dans une application Xamarin. Mac, vous avez accès aux mêmes alertes que celles d’un développeur qui travaille en *objective-C* et *Xcode* . 

Une alerte est un type spécial de dialogue qui s’affiche lorsqu’un problème sérieux se produit (par exemple, une erreur) ou en tant qu’avertissement (par exemple, la préparation de la suppression d’un fichier). Comme une alerte est une boîte de dialogue, elle nécessite également une réponse de l’utilisateur avant de pouvoir être fermée.

[![Un exemple d’alerte](alert-images/alert06.png)](alert-images/alert06.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation des alertes dans une application Xamarin. Mac. 

<a name="Introduction_to_Alerts"></a>

## <a name="introduction-to-alerts"></a>Présentation des alertes

Une alerte est un type spécial de dialogue qui s’affiche lorsqu’un problème sérieux se produit (par exemple, une erreur) ou en tant qu’avertissement (par exemple, la préparation de la suppression d’un fichier). Étant donné que les alertes perturbent l’utilisateur car elles doivent être rejetées avant que l’utilisateur puisse continuer à utiliser leur tâche, évitez d’afficher une alerte, sauf si elle est absolument nécessaire.

Apple suggère les instructions suivantes :

- N’utilisez pas d’alerte simplement pour fournir des informations aux utilisateurs.
- N’affiche pas d’alerte pour les actions courantes et annulables. Même si cette situation peut entraîner une perte de données.
- Si une situation est digne d’une alerte, évitez d’utiliser un autre élément ou une autre méthode d’interface utilisateur pour l’afficher.
- L’icône d’avertissement doit être utilisée avec modération.
- Décrivez clairement et succinctement la situation de l’alerte dans le message d’alerte.
- Le nom du bouton par défaut doit correspondre à l’action que vous décrivez dans votre message d’alerte.

Pour plus d’informations, consultez la section [alertes](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1) des guides de l' [interface utilisateur d’Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert"></a>

## <a name="anatomy-of-an-alert"></a>Anatomie d’une alerte

Comme indiqué ci-dessus, les alertes doivent être affichées à l’utilisateur de votre application lorsqu’un problème grave se produit ou en tant qu’avertissement pour une perte de données potentielle (par exemple, la fermeture d’un fichier non enregistré). Dans Xamarin. Mac, une alerte est créée dans le code C#, par exemple :

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Critical,
  InformativeText = "We need to save the document here...",
  MessageText = "Save Document",
};
alert.RunModal ();
```

Le code ci-dessus affiche une alerte avec l’icône applications superposée sur l’icône d’avertissement, un titre, un message d’avertissement et un seul bouton **OK** :

[![Une alerte avec un bouton OK](alert-images/alert01.png)](alert-images/alert01.png#lightbox)

Apple fournit plusieurs propriétés qui peuvent être utilisées pour personnaliser une alerte :

- **AlertStyle** définit le type d’une alerte comme l’un des éléments suivants :
  - **Avertissement** : permet d’avertir l’utilisateur d’un événement en cours ou imminent qui n’est pas critique. Il s'agit du style par défaut.
  - **Informatif** -utilisé pour avertir l’utilisateur de l’événement en cours ou imminent. Actuellement, il n’y a aucune différence visible entre un **Avertissement** et une **information**
  - **Critique** : permet d’avertir l’utilisateur des conséquences graves d’un événement imminent (par exemple, la suppression d’un fichier). Ce type d’alerte doit être utilisé avec modération.
- **MessageText** : message principal ou titre de l’alerte et doit rapidement définir la situation pour l’utilisateur.
- **InformativeText** : il s’agit du corps de l’alerte dans laquelle vous devez définir la situation de façon claire et présenter les options réalisables à l’utilisateur.
- **Icône** : permet à l’utilisateur d’afficher une icône personnalisée.
- **HelpAnchor**  &  **ShowsHelp** : permet à l’alerte d’être liée à l’application HelpBook et d’afficher l’aide pour l’alerte.
- **Boutons** : par défaut, une alerte ne contient que le bouton **OK** , mais la collection de **boutons** vous permet d’ajouter d’autres choix en fonction des besoins.
- **ShowsSuppressionButton** : si `true` affiche une case à cocher que l’utilisateur peut utiliser pour supprimer l’alerte pour les occurrences ultérieures de l’événement qui l’a déclenchée.
- **AccessoryView** : vous permet d’attacher une autre sous-vue à l’alerte pour fournir des informations supplémentaires, telles que l’ajout d’un **champ de texte** pour l’entrée de données. Si vous définissez un nouveau **AccessoryView** ou si vous en modifiez un, vous devez appeler la `Layout()` méthode pour ajuster la disposition visible de l’alerte.

<a name="Displaying_an_Alert"></a>

## <a name="displaying-an-alert"></a>Affichage d’une alerte

Il existe deux façons différentes d’afficher une alerte, à virgule flottante ou en tant que feuille. Le code suivant affiche une alerte sous forme de virgule flottante :

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.RunModal ();
```

Si ce code est exécuté, les éléments suivants s’affichent :

[![Une alerte simple](alert-images/alert02.png)](alert-images/alert02.png#lightbox)

Le code suivant affiche la même alerte qu’une feuille :

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

Si ce code est exécuté, les éléments suivants s’affichent :

[![Une alerte affichée sous forme de feuille](alert-images/alert03.png)](alert-images/alert03.png#lightbox)

<a name="Working_with_Alert_Buttons"></a>

## <a name="working-with-alert-buttons"></a>Utilisation des boutons d’alerte

Par défaut, une alerte affiche uniquement le bouton **OK** . Toutefois, vous n’êtes pas limité à cela, vous pouvez créer des boutons supplémentaires en les ajoutant à la collection de **boutons** . Le code suivant crée une alerte à virgule flottante avec un bouton **OK**, **Annuler** et **peut-être** :

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
var result = alert.RunModal ();
```

Le tout premier bouton ajouté est le _bouton par défaut_ qui est activé si l’utilisateur appuie sur la touche entrée. La valeur retournée est un entier représentant le bouton sur lequel l’utilisateur a appuyé. Dans notre cas, les valeurs suivantes sont renvoyées :

- **OK** -1000.
- **Annuler** -1001.
- **Peut-être** -1002.

Si nous exécutons le code, les éléments suivants s’affichent :

[![Une alerte avec trois options de bouton](alert-images/alert04.png)](alert-images/alert04.png#lightbox)

Voici le code de la même alerte qu’une feuille :

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}", result);
});
```

Si ce code est exécuté, les éléments suivants s’affichent :

[![Alerte à trois boutons affichée sous forme de feuille](alert-images/alert05.png)](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> Vous ne devez jamais ajouter plus de trois boutons à une alerte.

<a name="Showing_the_Suppress_Button"></a>

## <a name="showing-the-suppress-button"></a>Indication du bouton supprimer

Si la propriété de l’alerte `ShowSuppressButton` est `true` , l’alerte affiche une case à cocher que l’utilisateur peut utiliser pour supprimer l’alerte pour les occurrences ultérieures de l’événement qui l’a déclenchée. Le code suivant affiche une alerte flottante libre avec un bouton supprimer :

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Si la valeur de `alert.SuppressionButton.State` est `NSCellStateValue.On` , l’utilisateur a activé la case à cocher Supprimer, sinon ce n’est pas le cas.

Si le code est exécuté, les éléments suivants s’affichent :

[![Alerte avec un bouton supprimer](alert-images/alert06.png)](alert-images/alert06.png#lightbox)

Voici le code de la même alerte qu’une feuille :

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Si ce code est exécuté, les éléments suivants s’affichent :

[![Une alerte avec un bouton supprimer apparaît sous forme de feuille](alert-images/alert07.png)](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView"></a>

## <a name="adding-a-custom-subview"></a>Ajout d’une sous-vue personnalisée

Les alertes ont une `AccessoryView` propriété qui peut être utilisée pour personnaliser davantage l’alerte et ajouter des éléments comme un **champ de texte** pour l’entrée utilisateur. Le code suivant crée une alerte à virgule flottante avec un champ d’entrée de texte ajouté :

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
AlertStyle = NSAlertStyle.Informational,
InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Les lignes de clé ici vous `var input = new NSTextField (new CGRect (0, 0, 300, 20));` permettront de créer un **champ de texte** pour ajouter l’alerte. `alert.AccessoryView = input;` qui attache le **champ de texte** à l’alerte et l’appel à la `Layout()` méthode, qui est requis pour redimensionner l’alerte pour l’ajuster à la nouvelle sous-vue.

Si nous exécutons le code, les éléments suivants s’affichent :

[![Si nous exécutons le code, les éléments suivants s’affichent](alert-images/alert08.png)](alert-images/alert08.png#lightbox)

Voici la même alerte qu’une feuille :

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Si nous exécutons ce code, les éléments suivants s’affichent :

[![Alerte avec un affichage personnalisé](alert-images/alert09.png)](alert-images/alert09.png#lightbox)

<a name="Summary"></a>

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des alertes dans une application Xamarin. Mac. Nous avons vu les différents types et utilisations des alertes, comment créer et personnaliser des alertes et comment utiliser des alertes dans du code C#.

## <a name="related-links"></a>Liens associés

- [MacWindows (exemple)](/samples/xamarin/mac-samples/macwindows)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilisation de Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Présentation de Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)