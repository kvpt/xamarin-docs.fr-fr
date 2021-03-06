---
title: Afficher les fenêtres contextuelles
description: 'Xamarin.Forms fournit trois éléments d’interface utilisateur de type popup : une alerte, une feuille d’action et une invite. Cet article montre comment utiliser les API d’alerte, de feuille d’action et d’invite pour afficher des boîtes de dialogue qui demandent aux utilisateurs des questions simples, guident les utilisateurs par le biais de tâches et affichent des invites.'
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 09cffb4e5c7d8f6b78d5ab1de6ec9839c3969e87
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940107"
---
# <a name="display-pop-ups"></a>Afficher les fenêtres contextuelles

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

L’affichage d’une alerte, la demande d’un utilisateur ou l’affichage d’une invite est une tâche courante de l’interface utilisateur. Xamarin.Forms a trois méthodes sur la [`Page`](xref:Xamarin.Forms.Page) classe pour interagir avec l’utilisateur via une fenêtre contextuelle : [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) , [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) et `DisplayPromptAsync` . Elles sont affichées avec les contrôles natifs appropriés sur chaque plateforme.

## <a name="display-an-alert"></a>Afficher une alerte

Toutes les Xamarin.Forms plateformes prises en charge disposent d’une fenêtre contextuelle modale pour avertir l’utilisateur ou poser des questions simples. Pour afficher ces alertes dans Xamarin.Forms , utilisez la [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) méthode sur Any [`Page`](xref:Xamarin.Forms.Page) . La ligne de code suivante présente un message simple à l’utilisateur :

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

![Boîte de dialogue d’alerte avec un bouton](pop-ups-images/alert.png)

Cet exemple ne recueille aucune information de l’utilisateur. L’alerte s’affiche de façon modale, et une fois qu’elle est fermée l’utilisateur continue à interagir avec l’application.

La [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) méthode peut également être utilisée pour capturer la réponse d’un utilisateur en présentant deux boutons et en retournant un `boolean` . Pour obtenir une réponse à partir d’une alerte, fournissez du texte pour les deux boutons et attendez (`await`) la méthode. Une fois que l’utilisateur a sélectionné l’une des options, la réponse est retournée à votre code. Notez les mots clés `async` et `await` dans l’exemple de code ci-dessous :

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![Boîte de dialogue d’alerte avec deux boutons](pop-ups-images/alert2-sml.png)](pop-ups-images/alert2.png#lightbox)

La [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) méthode a également des surcharges qui acceptent un [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) argument qui spécifie la direction dans laquelle les éléments d’interface utilisateur sont acheminés dans l’alerte. Pour plus d’informations sur le sens du déroulement, consultez [localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

> [!WARNING]
> Par défaut, sur UWP, lorsqu’une alerte est affichée, toutes les clés d’accès définies sur la page derrière l’alerte peuvent toujours être activées. Pour plus d’informations, consultez [clés d’accès VisualElement sur Windows](~/xamarin-forms/platform/windows/visualelement-access-keys.md).

## <a name="guide-users-through-tasks"></a>Guider les utilisateurs à travers les tâches

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) est un élément d’interface utilisateur courant dans iOS. La Xamarin.Forms [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) méthode vous permet d’inclure ce contrôle dans des applications multiplateformes, en rendant les alternatives natives dans Android et UWP.

Pour afficher une feuille d’action, attendez (`await` [) `DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) dans n’importe quelle [`Page`](xref:Xamarin.Forms.Page), en passant les étiquettes de message et de bouton sous forme de chaînes. La méthode retourne l’étiquette de chaîne du bouton sur lequel l’utilisateur a cliqué. Voici un exemple simple :

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![Boîte de dialogue ActionSheet](pop-ups-images/action.png)

Le bouton `destroy` est restitué différemment des autres et peut être laissé `null` ou spécifié en tant que troisième paramètre de chaîne. L’exemple suivant utilise le bouton `destroy` :

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Boîte de dialogue de la feuille d’action avec le bouton détruire")](pop-ups-images/action2.png#lightbox "Boîte de dialogue de la feuille d’action avec le bouton détruire")

La [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) méthode a également une surcharge qui accepte un [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) argument qui spécifie la direction dans laquelle les éléments d’interface utilisateur sont acheminés dans la feuille d’action. Pour plus d’informations sur le sens du déroulement, consultez [localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="display-a-prompt"></a>Afficher une invite

Pour afficher une invite, appelez `DisplayPromptAsync` dans Any [`Page`](xref:Xamarin.Forms.Page) , en passant un titre et un message comme `string` arguments :

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

L’invite s’affiche de façon modale :

[![Capture d’écran d’une invite modale, sur iOS et Android](pop-ups-images/simple-prompt.png "Invite modale")](pop-ups-images/simple-prompt-large.png#lightbox "Invite modale")

Si vous avez cliqué sur le bouton OK, la réponse entrée est retournée en tant que `string` . Si vous avez cliqué sur le bouton Annuler, `null` est retourné.

La liste complète des arguments pour la `DisplayPromptAsync` méthode est la suivante :

- `title`, de type `string` , est le titre à afficher dans l’invite de commandes.
- `message`, de type `string` , est le message à afficher dans l’invite de commandes.
- `accept`, de type `string` , est le texte du bouton accepter. Il s’agit d’un argument facultatif, dont la valeur par défaut est OK.
- `cancel`, de type `string` , est le texte du bouton Annuler. Il s’agit d’un argument facultatif, dont la valeur par défaut est Cancel.
- `placeholder`, de type `string` , est le texte d’espace réservé à afficher dans l’invite de commandes. Il s’agit d’un argument facultatif, dont la valeur par défaut est `null` .
- `maxLength`, de type `int` , est la longueur maximale de la réponse de l’utilisateur. Il s’agit d’un argument facultatif, dont la valeur par défaut est-1.
- `keyboard`, de type `Keyboard` , est le type de clavier à utiliser pour la réponse de l’utilisateur. Il s’agit d’un argument facultatif, dont la valeur par défaut est `Keyboard.Default` .
- `initialValue`, de type `string` , est une réponse prédéfinie qui s’affiche et qui peut être modifiée. Il s’agit d’un argument facultatif, dont la valeur par défaut est un vide `string` .

L’exemple suivant illustre la définition de certains des arguments facultatifs :

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", initialValue: "10", maxLength: 2, keyboard: Keyboard.Numeric);
```

Ce code affiche une réponse prédéfinie de 10, limite le nombre de caractères pouvant être entrés à 2, et affiche le clavier numérique pour les entrées utilisateur :

[![Capture d’écran d’une invite modale facultative, sur iOS et Android](pop-ups-images/keyboard-prompt.png "Invite modale")](pop-ups-images/keyboard-prompt-large.png#lightbox "Invite modale")

> [!WARNING]
> Par défaut, sur UWP, quand une invite s’affiche, toutes les clés d’accès définies sur la page derrière l’invite peuvent toujours être activées. Pour plus d’informations, consultez [clés d’accès VisualElement sur Windows](~/xamarin-forms/platform/windows/visualelement-access-keys.md).

## <a name="related-links"></a>Liens connexes

- [PopupsSample](/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
- [Localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
